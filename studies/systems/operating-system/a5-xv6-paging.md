---
layout: page
title: "A5 xv6 Paging 과제"
permalink: /studies/systems/operating-system/a5-xv6-paging/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/A5%20xv6%20Paging%20%EA%B3%BC%EC%A0%9C.md)

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


---

이전: [A4 xv6 Free List 과제](a4-xv6-free-list.md) · 다음: [A6 xv6 Thread 과제](a6-xv6-thread.md)
