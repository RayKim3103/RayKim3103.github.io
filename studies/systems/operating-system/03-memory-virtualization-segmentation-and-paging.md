---
layout: page
title: "03. 메모리 가상화 - 세그먼테이션과 페이징"
permalink: /studies/systems/operating-system/03-memory-virtualization-segmentation-and-paging/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/03%20%EB%A9%94%EB%AA%A8%EB%A6%AC%20%EA%B0%80%EC%83%81%ED%99%94%20-%20%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%85%8C%EC%9D%B4%EC%85%98%EA%B3%BC%20%ED%8E%98%EC%9D%B4%EC%A7%95.md)

{% raw %}
tags: #operating-system #virtual-memory #segmentation #paging #tlb #page-fault

관련 노트: [운영체제 개요](01-os-overview.md), [A4 xv6 Free List 과제](a4-xv6-free-list.md), [A5 xv6 Paging 과제](a5-xv6-paging.md)

## 핵심 요약

메모리 가상화는 각 process가 자신만의 연속적인 address space를 가진 것처럼 보이게 한다. 실제 물리 메모리는 여러 process와 kernel이 공유하지만, MMU와 page table이 virtual address를 physical address로 변환해 isolation과 protection을 제공한다.

이 장은 C/C++ 메모리 오류, stack과 heap, segmentation, free space management, paging, page table entry, TLB, multi-level page table, page fault와 page replacement까지 다룬다.

## Process Address Space

Process의 address space는 code, data, heap, stack 등 여러 영역으로 나뉜다.

- Code segment: 실행할 명령어
- Data segment: 전역 변수와 정적 데이터
- Heap: `malloc()` 등으로 동적 할당되는 영역
- Stack: 함수 호출, local variable, return address를 저장하는 영역

Stack과 heap은 runtime에 동적으로 변한다. Stack은 보통 함수 호출이 깊어질수록 자라고, heap은 allocation 요청에 따라 커진다.

## C/C++ 메모리 오류

운영체제의 메모리 보호는 흔한 프로그래밍 오류를 이해하는 데도 중요하다.

- Memory leak: heap block을 할당하고 해제하지 않아 더 이상 접근할 수 없는 메모리가 남는다.
- Segmentation fault: 할당되지 않았거나 접근 권한이 없는 주소에 접근한다.
- Buffer overflow: 할당된 영역 밖을 읽거나 쓴다.
- Uninitialized memory: 초기화되지 않은 값에 의존한다.
- Dangling pointer: 해제된 메모리를 가리키는 pointer를 계속 사용한다.
- Double free: 같은 block을 두 번 해제한다.

`valgrind`는 leak과 invalid access를 찾는 데 유용하고, `gdb`는 segmentation fault의 위치와 call stack을 추적하는 데 도움이 된다.

## Virtual-to-Physical Address Translation

CPU가 내는 주소는 virtual address이고, 실제 DRAM 위치는 physical address다. MMU는 OS가 설정한 translation data structure를 이용해 virtual address를 physical address로 바꾼다.

주소 변환의 목표:

- Transparency: process는 실제 물리 배치를 몰라도 된다.
- Efficiency: 주소 변환이 너무 느리면 안 된다.
- Protection: process가 허용되지 않은 memory에 접근하지 못해야 한다.

## Segmentation

Segmentation은 address space를 code, heap, stack 같은 가변 크기 segment로 나누고, segment마다 base와 bounds를 두는 방식이다.

주소 변환:

```text
physical address = base + offset
```

단, offset이 segment bounds를 넘으면 protection fault가 발생한다.

Segmentation의 장점은 실제 사용하는 영역만 물리 메모리에 배치할 수 있다는 점이다. 그러나 segment 크기가 가변이라 external fragmentation이 생긴다. 빈 공간이 총량으로는 충분해도 연속된 큰 공간이 없으면 allocation이 실패할 수 있다.

## 숫자로 확인하기 — Segmentation 주소 변환과 fault

heap segment의 base가 `0x4000`, bounds(크기)가 `0x1000`이라고 하면, 유효한 offset은 `0`~`0xFFF`다. Virtual offset `0x500`에 접근하면

$$
\text{physical address} = \text{base} + \text{offset} = 0\text{x}4000 + 0\text{x}500 = 0\text{x}4500
$$

정상적으로 변환된다. 하지만 offset `0x1200`으로 접근하면 `0x1200 > 0x1000`이므로 bounds를 벗어나 **protection fault**가 발생한다 — segmentation fault는 바로 이 bounds 검사 실패에서 나온다.

## 숫자로 확인하기 — Paging 주소 분할

32비트 virtual address, page size 4KB($$2^{12}$$byte)인 시스템을 가정하면 offset 필드는 12비트, 남은 20비트가 VPN(virtual page number)이다.

Virtual address `0x00403004`를 2진수로 보면 하위 12비트가 offset, 상위 20비트가 VPN이다.

$$
0\text{x}00403004 = 0000\ 0000\ 0100\ 0000\ 0011\ 0000\ 0000\ 0100_2
$$

하위 12비트 `0000\ 0000\ 0100` = `0x004`가 page 내부 offset, 상위 20비트가 VPN `0x00403`이 된다. 만약 VPN `0x00403`이 physical frame `0x00007`에 mapping되어 있다면, offset은 그대로 유지되어

$$
\text{physical address} = (0\text{x}00007 \ll 12) \,|\, 0\text{x}004 = 0\text{x}00007004
$$

가 된다 — VPN만 바뀌고 offset은 변환 전후 동일하게 유지된다는 사실이 이 계산에서 그대로 보인다.

## 숫자로 확인하기 — Page Replacement (FIFO vs LRU)

Physical frame 3개, page reference string `1,2,3,4,1,2,5,1,2,3,4,5`에 대해 FIFO와 LRU를 비교하면 page fault 수가 달라진다.

**FIFO**(삽입 순서 큐로만 제거를 결정, hit이 나도 순서를 바꾸지 않음): `1,2,3` fault(frame:1,2,3), `4` fault·1 제거(frame:2,3,4), `1` fault·2 제거(frame:3,4,1), `2` fault·3 제거(frame:4,1,2), `5` fault·4 제거(frame:1,2,5), `1` **hit**(frame 유지), `2` **hit**(frame 유지), `3` fault·1 제거(frame:2,5,3), `4` fault·2 제거(frame:5,3,4), `5` **hit**. Fault 발생 횟수를 세면 1,2,3,4,1,2,5,3,4 = **9회**, hit은 8,9,12번째 참조(1,2,5) = 3회.

**LRU**(가장 최근 사용 시점 기준으로 제거를 결정, hit마다 그 page가 최근으로 갱신됨): `1,2,3,4` fault까지는 FIFO와 동일(frame:2,3,4). `1` fault, LRU인 2 제거(frame:3,4,1). `2` fault, LRU인 3 제거(frame:4,1,2). `5` fault, LRU인 4 제거(frame:1,2,5). `1` **hit**(사용 순서를 2,5,1로 갱신). `2` **hit**(사용 순서를 5,1,2로 갱신). `3` fault, 이번엔 사용 순서상 가장 오래된 5를 제거(frame:1,2,3). `4` fault, 가장 오래된 1을 제거(frame:2,3,4). `5` fault, 가장 오래된 2를 제거(frame:3,4,5). Fault 횟수 = 1,2,3,4,5,6,7,10,11,12번째 참조 = **10회**, hit은 8,9번째(1,2)뿐 = 2회.

이 특정 참조열에서는 FIFO(9회)가 LRU(10회)보다 오히려 fault가 적다 — "미래를 더 잘 근사하는 정책(LRU)이 항상 더 단순한 정책(FIFO)보다 우월한 것은 아니다"라는 반직관적인 사실을 보여준다. 두 정책 모두 hit 시점의 page 2개(`1`,`2`)는 공유하지만, 그 hit이 다음 fault의 희생양을 어떻게 바꾸는지가 갈라지는 지점이다. 알고리즘의 우열은 참조 패턴에 따라 달라지므로 항상 실제 workload로 확인해야 한다.

## Free Space Management

Heap이나 물리 메모리에서 빈 공간을 관리하려면 free list가 필요하다. 각 free block은 크기와 다음 free block pointer를 가진다.

대표 policy:

- First-fit: 처음 발견한 충분히 큰 block을 사용한다.
- Best-fit: 요청 크기에 가장 가까운 작은 block을 사용한다.
- Worst-fit: 가장 큰 free block을 사용해 남는 조각을 크게 유지하려 한다.
- Next-fit: 이전 검색 위치부터 이어서 찾는다.

Allocation 후 block이 더 크면 split하고, free 후 인접 free block은 coalescing해 fragmentation을 줄인다.

## Paging

Paging은 virtual address space와 physical memory를 고정 크기 page/frame으로 나눈다. Virtual page는 page table을 통해 physical frame에 mapping된다.

주소는 다음처럼 나뉜다.

```text
virtual address = virtual page number + page offset
physical address = physical frame number + page offset
```

Page offset은 page 내부 위치이므로 변환 후에도 그대로 유지된다. Paging은 external fragmentation을 없애고 allocation을 단순하게 만들지만, page table memory overhead와 internal fragmentation이 생길 수 있다.

## Page Table Entry

PTE는 virtual page가 어떤 physical frame에 대응되는지와 접근 권한을 저장한다. RISC-V/xv6 기준으로 생각하면 valid bit, readable, writable, executable, user 접근 가능 여부 같은 flag가 중요하다.

주요 flag:

- Valid: mapping이 유효한가
- Read/Write/Execute: 접근 권한
- User: user mode 접근 허용 여부
- Accessed/Dirty: page replacement와 write-back 판단에 사용 가능

## TLB

Page table walk는 매 memory access마다 수행하기에는 비싸다. TLB(Translation Lookaside Buffer)는 최근 translation을 caching한다.

- TLB hit: page table walk 없이 빠르게 변환한다.
- TLB miss: page table을 읽어 translation을 찾고 TLB에 채운다.
- Context switch: address space가 바뀌므로 TLB flush 또는 ASID가 필요하다.

TLB는 virtual memory의 성능을 좌우하는 핵심 cache다.

## Multi-Level Page Table

단일 page table은 virtual address space가 크면 너무 많은 메모리를 차지한다. Multi-level page table은 page directory와 하위 page table을 계층적으로 두어 실제로 사용하는 영역의 page table만 할당한다.

Page table walk는 virtual address의 여러 index field를 차례로 사용한다. 상위 level entry가 invalid이면 하위 page table 자체가 없다고 보고 fault 처리한다. Sparse address space에서는 multi-level 구조가 큰 메모리 절약을 준다.

## Page Fault

Page fault는 virtual address에 대한 유효한 mapping이 없거나 권한이 맞지 않을 때 발생한다. OS의 page fault handler는 fault 원인과 주소를 확인한 뒤 처리한다.

가능한 처리:

- 잘못된 접근이면 process를 종료한다.
- Lazy allocation이면 새 physical frame을 할당하고 mapping한다.
- Page가 disk에 있으면 읽어와 mapping한다.
- Copy-on-write이면 writable copy를 만든다.

Page fault는 오류일 수도 있고, OS가 memory virtualization을 효율적으로 구현하기 위한 정상적인 mechanism일 수도 있다.

## Page Replacement

물리 메모리가 부족하면 OS는 어떤 page를 내보낼지 선택해야 한다. 이상적인 policy는 앞으로 가장 늦게 쓰일 page를 제거하는 OPT지만 미래를 알 수 없다.

현실적인 policy:

- FIFO: 오래된 page 제거
- LRU: 가장 오래 사용되지 않은 page 제거
- Clock: reference bit를 이용한 LRU 근사

## 복습 체크포인트

- Stack과 heap의 역할과 동적 성장 방향을 설명할 수 있는가?
- Segmentation의 base/bounds 변환과 fragmentation 문제를 말할 수 있는가?
- Paging에서 VPN, PFN, offset을 분리해 주소 변환을 수행할 수 있는가?
- PTE flag가 protection에 어떻게 쓰이는가?
- TLB hit/miss와 page fault의 차이를 설명할 수 있는가?
- Multi-level page table이 sparse address space에서 메모리를 줄이는 이유를 설명할 수 있는가?
- base=0x4000, bounds=0x1000인 segment에서 offset 0x1200이 왜 protection fault를 일으키는지 계산할 수 있는가?
- 32비트 주소 `0x00403004`에서 4KB page 기준 VPN과 offset을 직접 분리할 수 있는가?
- 위 12-참조 예제에서 FIFO가 LRU보다 fault가 적게 나오는 것을 직접 추적해, "LRU가 항상 우월하지는 않다"는 결론을 확인할 수 있는가?

{% endraw %}

---

이전: [02. CPU 가상화 - 프로세스와 스케줄링](02-cpu-virtualization-processes-and-scheduling.md) · 다음: [04. 멀티스레딩](04-multithreading.md)
