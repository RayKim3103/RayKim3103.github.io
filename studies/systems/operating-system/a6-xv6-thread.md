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

## 숫자로 확인하기 — thread stack 주소 배치

Page 크기 4KB, `NPROC=64`라고 하면 예약해야 할 stack 영역은 $$NPROC+1=65$$ page다. Address space의 top(예: user stack이 시작하는 주소 `TRAPFRAME` 바로 아래)을 기준으로 각 thread와 guard page를 아래 방향으로 배치하면:

$$
\text{thread}_i\text{의 stack top} = \text{TOP} - (i+1) \times (\text{guard page} + \text{stack page}) = \text{TOP} - (i+1) \times 8\text{KB}
$$

즉 thread 0의 stack은 `TOP-8KB`부터, thread 1은 `TOP-16KB`부터, thread 2는 `TOP-24KB`부터 시작하는 식으로 **한 thread당 8KB(guard page 4KB + stack page 4KB)씩 아래로 밀려난다**. 전체 65개 slot을 이렇게 배치하면 필요한 주소 범위는

$$
65 \times 8\text{KB} = 520\text{KB}
$$

이다. 만약 guard page 없이 stack page만 다닥다닥 붙여 배치했다면, 한 thread가 자기 stack 아래로 넘치는(overflow) 순간 바로 옆 thread의 stack을 조용히 손상시킬 수 있다 — guard page가 그 접근을 즉시 page fault로 만들어 버그를 눈에 띄게 하는 역할을 한다는 것이 이 배치 계산에서 드러난다.

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

## 복습 체크포인트

- 위 예제에서 thread 2의 stack이 왜 `TOP-24KB`에서 시작하는지, 8KB 간격이 어디서 나오는지 계산할 수 있는가?
- Guard page가 없다면 stack overflow가 왜 다른 thread의 stack을 조용히 손상시킬 수 있는지 설명할 수 있는가?
- `fork()`(주소 공간 복사)와 `tfork()`(주소 공간 공유) 중 어느 쪽이 이번 thread stack 배치 문제와 관련 있는지, 그리고 그 이유를 설명할 수 있는가?

{% endraw %}

---

이전: [A5 xv6 Paging 과제](a5-xv6-paging.md)
