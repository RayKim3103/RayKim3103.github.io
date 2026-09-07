---
layout: page
title: "A5 xv6 Paging 과제"
permalink: /studies/systems/operating-system/a5-xv6-paging/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/A5%20xv6%20Paging%20%EA%B3%BC%EC%A0%9C.md)

{% raw %}
tags: #operating-system #xv6 #paging #page-fault #copy-on-write

관련 노트: [메모리 가상화 - 세그먼테이션과 페이징](03-memory-virtualization-segmentation-and-paging.md), [A4 xv6 Free List 과제](a4-xv6-free-list.md)

## 과제 목표

이 과제는 xv6-riscv의 page allocation과 page fault 처리를 수정해 더 효율적인 paging 동작을 구현하는 것이다. PDF는 새 page를 zero frame에 mapping하고, write가 발생하면 dirty page를 새 frame으로 remap하는 흐름을 설명한다. 이는 lazy allocation 또는 copy-on-write 계열의 사고방식과 연결된다.

## 기본 Page Allocation 흐름

xv6에서 process address space 확장은 보통 `uvmalloc()`을 통해 이루어진다. 기존 방식은 필요한 virtual page마다 physical frame을 할당하고 page table에 mapping한다.

과제의 핵심 변화는 새 page들을 처음부터 각각의 physical frame에 할당하지 않고, 공통 zero frame에 mapping할 수 있다는 점이다. 읽기만 하는 동안에는 모든 page가 같은 zero content를 보아도 의미가 같다.

## Zero Frame Mapping

새로 할당된 page는 내용이 0이어야 한다. 여러 virtual page를 read-only zero frame에 mapping하면 실제 physical frame allocation을 늦출 수 있다.

장점:

- 실제 write가 없는 page에는 physical memory를 쓰지 않는다.
- 큰 address space를 만들 때 초기 allocation 비용을 줄인다.
- Demand paging의 기본 아이디어를 실습할 수 있다.

주의:

- Zero frame은 read-only여야 한다.
- User가 write하려고 하면 page fault가 발생해야 한다.
- Page table flag를 정확히 설정해야 한다.

## Store Page Fault 처리

RISC-V에서 store page fault가 발생하면 `scause` register가 fault 원인을 알려주고, `stval` register가 fault가 난 virtual address를 담는다. xv6에서는 `r_stval()` 같은 helper로 faulty address를 읽을 수 있다.

Handler의 일반 흐름:

1. `scause`가 store page fault인지 확인한다.
2. `stval`에서 faulting virtual address를 얻는다.
3. Page boundary에 맞게 round down한다.
4. Page table walk로 PTE를 찾는다.
5. 해당 page가 zero frame에 read-only로 mapping된 page인지 확인한다.
6. 새 physical frame을 할당하고 0으로 초기화한다.
7. PTE를 새 frame으로 바꾸고 writable flag를 켠다.
8. 잘못된 fault라면 process를 kill한다.

## Page Table Flag 관리

이 과제의 난점은 "어떤 read-only fault가 정상적인 zero-page write인지" 구분하는 것이다. 모든 write fault를 새 page allocation으로 처리하면 protection bug가 된다. 따라서 PTE flag나 별도 marker로 zero frame mapping 여부를 구분해야 한다.

확인할 flag:

- Valid bit
- User bit
- Read/Write/Execute permission
- Dirty 또는 custom marker로 활용 가능한 bit

RISC-V PTE에서 사용 가능한 reserved/software bit가 있다면 이를 marker로 쓸 수 있지만, skeleton의 의도를 따라야 한다.

## Validation 해석

Validation은 새 page들이 처음에는 zero frame에 mapping되고, write가 일어난 page만 새 frame으로 remap되는지 확인한다. Page fault 횟수와 memory allocation 수가 예상과 맞아야 한다.

검증 관점:

- 새로 확보한 memory를 읽으면 0이 나온다.
- 읽기만 하는 page는 새 physical frame을 소비하지 않는다.
- Write한 page는 독립 frame으로 바뀐다.
- 다른 virtual page의 값이 함께 바뀌지 않는다.
- Invalid address 접근은 정상 fault로 처리되어 process가 종료된다.

## 실수하기 쉬운 부분

- Zero frame을 writable로 mapping해 모든 page가 같은 physical memory를 공유하게 만드는 문제
- Fault address를 page boundary로 내리지 않는 문제
- Page table walk 실패를 정상 case처럼 처리하는 문제
- PTE flag를 갱신한 뒤 TLB flush를 고려하지 않는 문제
- Kernel address나 user address 범위 검사를 빠뜨리는 문제

## 복습 체크포인트

- Demand paging과 lazy allocation의 성능상 이점을 설명할 수 있는가?
- Store page fault에서 `scause`와 `stval`의 역할을 말할 수 있는가?
- Zero page를 read-only로 두어야 하는 이유는 무엇인가?
- Page fault handler가 정상 lazy allocation과 불법 접근을 어떻게 구분해야 하는가?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **A5 xv6 Paging 과제**를 다루며, process, scheduling, virtual memory, concurrency, file system을 통해 OS가 hardware resource를 추상화하고 보호하는 방식을 이해한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- OS 주제에서는 user/kernel 경계와 kernel data structure 변화가 어느 함수에서 일어나는지 추적한다.
- 동시성 오류는 특정 interleaving을 상상해 shared invariant가 깨지는 순간을 찾는 방식으로 접근한다.
- OS의 핵심은 CPU, memory, disk를 각각 process, address space, file이라는 추상화로 바꾸는 것이다.
- concurrency에서는 correctness가 성능보다 먼저이며 race, deadlock, starvation을 구분해야 한다.
- virtual memory는 isolation, relocation, demand paging, caching을 제공하지만 TLB/page fault 비용이 따른다.

### 문제 풀이 또는 구현 루틴

- kernel path를 user call, trap, syscall handler, kernel object update, return 순서로 추적한다.
- 동기화 문제는 shared state, invariant, lock ownership, sleep/wakeup 조건을 먼저 적는다.
- 파일 시스템은 inode, block allocation, directory entry, cache, crash consistency 순서로 본다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- process와 thread는 address space 공유 여부가 핵심 차이다.
- lock을 잡은 채 sleep하거나 다른 lock 순서를 섞으면 deadlock이 생길 수 있다.
- page table entry bit 하나가 protection, sharing, lazy allocation 동작을 바꾼다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 기능은 어떤 kernel data structure를 바꾸는가?
- 동시 실행될 때 invariant가 깨지는 interleaving은 없는가?
- 성능 병목이 context switch, page fault, lock contention, I/O 중 어디인가?
- **A5 xv6 Paging 과제**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [A4 xv6 Free List 과제](a4-xv6-free-list.md) · 다음: [A6 xv6 Thread 과제](a6-xv6-thread.md)
