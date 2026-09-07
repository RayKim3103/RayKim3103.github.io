---
layout: page
title: "A4 xv6 Free List 과제"
permalink: /studies/systems/operating-system/a4-xv6-free-list/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/A4%20xv6%20Free%20List%20%EA%B3%BC%EC%A0%9C.md)

{% raw %}
tags: #operating-system #xv6 #malloc #free-list #memory-allocation

관련 노트: [메모리 가상화 - 세그먼테이션과 페이징](03-memory-virtualization-segmentation-and-paging.md)

## 과제 목표

이 과제는 xv6-riscv user-level `malloc()`의 free list 관리 정책을 next-fit에서 worst-fit으로 바꾸는 것이다. Kernel page allocator가 아니라 `user/umalloc.c`의 heap allocator를 수정한다.

## xv6 malloc 구조

xv6의 `malloc()`은 header 단위로 memory block을 관리한다. 요청 byte 수를 `Header` 크기 단위로 올림하고, free list에서 충분한 free block을 찾는다.

기본 구조:

- `Header`: block size와 next pointer를 가진다.
- `base`: circular free list의 dummy node
- `freep`: 마지막으로 접근한 free block 근처를 가리키는 pointer
- `morecore()`: free list에 충분한 block이 없을 때 `sbrk()`로 heap을 늘린다.

Free block들은 singly linked circular list로 연결된다.

## 기존 Next-Fit 방식

기존 구현은 `freep` 다음 block부터 순회하면서 요청 크기 이상인 첫 block을 선택한다. 마지막으로 본 위치에서 다시 시작하므로 next-fit이다.

동작:

1. Free list가 없으면 dummy node를 만든다.
2. `freep->s.ptr`부터 순회한다.
3. 크기가 충분한 첫 block을 선택한다.
4. 정확히 같은 크기면 list에서 제거한다.
5. 더 크면 block 뒤쪽을 잘라 allocation한다.
6. 적당한 block이 없으면 `morecore()`로 heap을 늘린다.

## Worst-Fit 정책

Worst-fit은 매 allocation마다 free list 전체를 검색해 가장 큰 free block을 선택한다. 목표는 남는 block을 작게 쪼개기보다 가능한 큰 잔여 block을 유지하는 것이다.

구현 포인트:

- Free list를 끝까지 순회해야 한다.
- 요청 크기 이상인 후보 중 size가 가장 큰 block을 기억한다.
- 같은 크기의 후보가 여러 개면 아무거나 선택해도 된다.
- 선택 block의 previous pointer도 함께 저장해야 list 제거 또는 split이 가능하다.

## Split과 Exact Fit

선택한 block 크기가 요청 단위와 같으면 block 전체를 list에서 제거한다.

```text
prev->next = p->next
```

선택한 block이 더 크면 block size를 줄이고, 줄어든 block의 뒤쪽에 allocation block을 만든다. 기존 xv6 allocator는 block의 rear end를 잘라 반환하는 구조이므로 pointer arithmetic을 조심해야 한다.

## morecore와 sbrk

Free list에서 적절한 block을 찾지 못하면 `morecore(nunits)`를 호출한다. `morecore()`는 작은 요청이 자주 syscall을 부르지 않도록 최소 64KB 정도의 큰 block을 `sbrk()`로 확보한다. 이후 새 block을 `free()`에 넘겨 free list에 삽입하고 인접 block과 병합한다.

이 과제는 `malloc()` 수정이 중심이며, `morecore()`나 `free()`의 기존 coalescing 동작은 유지하는 방향이다.

## Debugging용 freelist()

과제에는 free list의 address, size, next pointer를 출력하는 `freelist()` helper가 제공된다. `malloctest`는 여러 allocation/free 후 free list 상태를 출력한다.

주의:

- `freelist()`와 `malloctest()`를 임시로 수정해 debugging할 수 있다.
- 제출 전에는 요구된 상태로 되돌려야 한다.
- Grading은 free list 출력 상태를 기반으로 한다.

## 검증 포인트

- 매 allocation에서 free list 전체를 검색한다.
- 가장 큰 후보 block을 선택한다.
- Exact fit에서 list link가 깨지지 않는다.
- Split 후 남은 block size와 반환 pointer가 맞다.
- 적절한 block이 없으면 `morecore()` 경로가 정상 동작한다.
- Circular list 순회가 무한 loop에 빠지지 않는다.

## 실수하기 쉬운 부분

- 가장 큰 block pointer만 저장하고 previous pointer를 저장하지 않는 문제
- `freep` 기준 순회 종료 조건을 잘못 잡는 문제
- Header 단위와 byte 단위를 혼동하는 문제
- Split 위치를 앞쪽으로 잡아 기존 xv6 pointer arithmetic과 어긋나는 문제
- Debug print나 helper 수정 사항을 제출에 남기는 문제

## 보강 학습 노트

### 큰 그림

- 이 문서는 **A4 xv6 Free List 과제**를 다루며, process, scheduling, virtual memory, concurrency, file system을 통해 OS가 hardware resource를 추상화하고 보호하는 방식을 이해한다.
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
- **A4 xv6 Free List 과제**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [A3 xv6 Scheduling 과제](a3-xv6-scheduling.md) · 다음: [A5 xv6 Paging 과제](a5-xv6-paging.md)
