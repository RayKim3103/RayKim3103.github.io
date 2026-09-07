---
layout: page
title: "A6 xv6 Thread 과제"
permalink: /studies/systems/operating-system/a6-xv6-thread/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/A6%20xv6%20Thread%20%EA%B3%BC%EC%A0%9C.md)

{% raw %}
tags: #operating-system #xv6 #thread #tfork #mutex #trapframe

관련 노트: [멀티스레딩](04-multithreading.md), [CPU 가상화 - 프로세스와 스케줄링](02-cpu-virtualization-processes-and-scheduling.md), [메모리 가상화 - 세그먼테이션과 페이징](03-memory-virtualization-segmentation-and-paging.md)

## 과제 목표

이 과제는 xv6-riscv에 user-level multithreading 기능을 추가하는 것이다. Baseline xv6에는 user process용 thread creation, join, exit, mutex 기능이 없으므로, `thread_create`, `thread_join`, `thread_exit`, mutex API를 구현한다.

## 제공되는 User Interface

`user/uthread.h`에는 다음과 같은 interface가 정의된다.

- `thread_create()`: 새 thread 생성
- `thread_join()`: 특정 thread 종료 대기 및 return value 수거
- `thread_exit()`: 현재 thread 종료
- `thread_mutex_init()`
- `thread_mutex_destroy()`
- `thread_mutex_lock()`
- `thread_mutex_unlock()`

`thread_mutex_t`는 lock 소유 여부를 나타내는 `locked` field를 가진 단순 구조로 시작한다.

## xv6에서 Thread를 Process Entry로 재사용

과제는 xv6의 `struct proc`와 `proc[]` entry를 thread 관리에도 재사용하게 한다. 즉 thread도 scheduler 입장에서는 schedulable entity로 보이지만, 일반 fork process와 달리 parent와 address space를 공유한다.

이 방식의 장점:

- 기존 scheduler와 context switch 대부분을 재사용할 수 있다.
- process lock, kernel stack, trapframe 개념을 그대로 활용한다.

차이점:

- `fork()`는 address space를 복사하지만 thread는 공유한다.
- Thread마다 user stack과 trapframe은 별도로 필요하다.
- File descriptor 공유와 reference count 처리에 주의해야 한다.

## tfork() System Call

`thread_create()`는 kernel의 새 syscall `tfork()`를 감싼 wrapper로 볼 수 있다. `tfork()`는 `fork()`와 비슷하지만 다음 부분이 다르다.

- 새 process slot을 잡고 PID를 배정한다.
- Parent page table을 복사하지 않고 공유한다.
- 새 thread용 trapframe을 준비한다.
- 새 thread의 user stack과 entry function, argument를 설정한다.
- Return path가 `fork()`처럼 caller 다음 줄이 아니라 thread function 시작점으로 이어져야 한다.

결과적으로 `thread_create()`는 `fork()`와 `exec()`의 일부 성격이 섞인 기능이다. 같은 program image를 공유하지만 새 execution entry를 지정한다.

## Trapframe과 Trampoline

Thread마다 user mode 실행 상태를 저장할 trapframe이 필요하다. xv6의 trampoline code는 user/kernel 전환 시 register를 저장하고 복원한다.

중요한 점:

- 각 thread가 같은 trapframe page를 공유하면 context가 덮어써진다.
- 새 thread의 trapframe은 parent와 다른 위치에 mapping되어야 한다.
- Trampoline mapping은 process address space에서 공통적으로 유지된다.

## User Stack 배치

Thread는 각자 user stack이 있어야 한다. 과제 설명은 heap 위쪽 또는 top address 근처에 `NPROC+1` pages를 예약하고, guard page와 thread stack pages를 배치하는 구조를 설명한다.

Stack 관련 주의:

- Parent stack pointer를 그대로 쓰면 thread들이 같은 stack을 공유해 망가진다.
- 각 thread는 고유한 stack page를 가져야 한다.
- Guard page는 stack overflow 탐지에 도움을 준다.
- Stack pointer는 ABI에 맞게 정렬해야 한다.

## thread_join()

`thread_join()`은 특정 thread가 종료될 때까지 기다리고 return value를 받아온다. 기존 `wait()`와 비슷하지만 process가 아니라 같은 address space를 공유하는 thread를 대상으로 한다.

처리할 점:

- 대상 thread가 존재하는지 확인한다.
- 대상 thread가 ZOMBIE가 될 때까지 sleep/wakeup으로 기다린다.
- 종료 값을 user pointer로 copyout한다.
- Thread의 kernel 자원과 stack/trapframe 관련 자원을 정리한다.

## thread_exit()

`thread_exit()`은 현재 thread를 종료하고 return value를 저장한 뒤 parent 또는 joiner를 깨운다. 일반 process exit과 달리 address space 전체를 해제하면 안 된다. 같은 process의 다른 thread가 아직 실행 중일 수 있기 때문이다.

즉 thread 종료와 process 종료의 자원 회수 범위가 다르다.

## Mutex 구현

Mutex는 shared address space에서 race condition을 막는다. User-level API는 `thread_mutex_lock()`과 `thread_mutex_unlock()`을 제공한다.

구현 관점:

- Atomic instruction 또는 xv6에서 제공되는 primitive를 사용해야 한다.
- 단순 busy-wait lock이면 correctness는 가능하지만 CPU를 낭비할 수 있다.
- Unlock은 waiting thread가 진행할 수 있게 상태를 바꿔야 한다.

과제 환경은 두 CPU로 설정되므로 이전 단일 CPU 과제와 달리 lock 조작이 실제 race condition을 막아야 한다.

## Multi-Core와 Lock 주의

PDF는 이전 과제에서는 single CPU라 process lock 효과가 거의 보이지 않았지만, 이 과제에서는 two CPU 설정이므로 lock을 조심해야 한다고 강조한다.

위험한 상황:

- 두 CPU가 동시에 같은 process/thread state를 바꾸는 경우
- Parent가 join으로 thread를 회수하는 동안 thread가 exit하는 경우
- Shared page table 또는 file descriptor reference count를 동시에 건드리는 경우

## 검증 포인트

- `threadtests`가 thread creation, join, return value를 검증한다.
- 여러 thread가 같은 address space를 공유한다.
- 각 thread는 독립 user stack을 사용한다.
- Mutex로 shared counter 같은 critical section을 보호할 수 있다.
- Thread exit이 process 전체를 죽이지 않는다.
- Race condition 없이 two CPU 환경에서 안정적으로 동작한다.

## 실수하기 쉬운 부분

- `uvmcopy()`로 address space를 복사해 thread가 아니라 process처럼 만들어버리는 문제
- Trapframe mapping을 thread별로 분리하지 않는 문제
- Thread stack을 잘못 배치해 stack 충돌이 나는 문제
- `wait()`와 `thread_join()`의 자원 회수 범위를 혼동하는 문제
- Thread 하나의 exit에서 shared page table을 해제하는 문제
- Mutex를 non-atomic load/store만으로 구현하는 문제

## 보강 학습 노트

### 큰 그림

- 이 문서는 **A6 xv6 Thread 과제**를 다루며, process, scheduling, virtual memory, concurrency, file system을 통해 OS가 hardware resource를 추상화하고 보호하는 방식을 이해한다.
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
- **A6 xv6 Thread 과제**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [A5 xv6 Paging 과제](a5-xv6-paging.md)
