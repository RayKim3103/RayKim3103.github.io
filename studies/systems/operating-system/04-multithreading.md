---
layout: page
title: "04. 멀티스레딩"
permalink: /studies/systems/operating-system/04-multithreading/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/04%20%EB%A9%80%ED%8B%B0%EC%8A%A4%EB%A0%88%EB%94%A9.md)

{% raw %}
tags: #operating-system #thread #race-condition #lock #condition-variable #semaphore

관련 노트: [운영체제 개요](01-os-overview.md), [CPU 가상화 - 프로세스와 스케줄링](02-cpu-virtualization-processes-and-scheduling.md), [A6 xv6 Thread 과제](a6-xv6-thread.md)

## 핵심 요약

멀티스레딩은 하나의 process 안에서 여러 실행 흐름이 같은 address space를 공유하며 실행되는 구조다. Thread는 각자 stack과 register context를 가지지만, heap과 global variable, open file 등 process 자원을 공유한다. 이 공유 때문에 race condition이 생기며, 이를 제어하기 위해 mutex, condition variable, semaphore, reader-writer lock을 사용한다.

이 장의 핵심은 다음 흐름이다.

- Thread 생성, join, exit의 POSIX API를 이해한다.
- Shared data 접근에서 critical section을 식별한다.
- Atomic instruction을 이용해 lock을 구현하는 원리를 본다.
- Condition variable로 조건 대기를 표현한다.
- Semaphore로 mutual exclusion과 ordering을 모두 표현할 수 있음을 이해한다.

## Process와 Thread

Multi-processing에서는 독립된 process들이 각자 address space를 가진다. Multi-threading에서는 여러 thread가 하나의 address space를 공유한다.

Thread별로 독립적인 것:

- Program counter
- Register context
- Stack
- Scheduling state

Thread들이 공유하는 것:

- Code segment
- Heap
- Global/static variables
- File descriptors
- Process-level resources

따라서 thread는 process보다 생성과 전환이 가벼울 수 있지만, shared memory synchronization이 필수다.

## POSIX Thread API

### pthread_create()

`pthread_create()`는 새 thread를 만들고 지정된 thread function을 실행한다. Thread function은 `void *`를 인자로 받고 `void *`를 반환하는 형태가 일반적이다.

각 thread의 local variable 주소가 서로 다르게 나타나는 것은 thread마다 stack이 따로 있음을 보여준다.

### pthread_join()

Thread를 만들었다고 main thread가 자동으로 기다리지는 않는다. `pthread_join()`은 특정 thread가 끝날 때까지 기다리고, thread function의 return value를 받을 수 있다.

### pthread_exit()

Thread function은 return으로 종료할 수 있고, 명시적으로 `pthread_exit()`을 호출할 수도 있다. 반환값을 `pthread_join()`에서 받으려면 pointer lifetime에 주의해야 한다. stack local variable 주소를 반환하면 thread 종료 후 dangling pointer가 된다.

## Race Condition

Race condition은 실행 순서에 따라 결과가 달라지는 오류다. 예를 들어 shared counter 증가 `counter++`는 하나의 C 문장처럼 보이지만 machine instruction 수준에서는 load, add, store로 나뉜다. 두 thread의 instruction이 섞이면 증가가 누락될 수 있다.

Race condition이 발생하는 영역을 critical section이라고 한다. Critical section에는 한 번에 하나의 thread만 들어가야 한다.

## Mutual Exclusion과 Lock

Mutex lock은 critical section을 보호한다.

기본 사용 패턴:

```c
pthread_mutex_lock(&lock);
// shared data 접근
pthread_mutex_unlock(&lock);
```

좋은 lock의 조건:

- Mutual exclusion: 동시에 두 thread가 critical section에 들어가지 않는다.
- Fairness: 특정 thread가 영원히 기다리지 않는다.
- Performance: lock overhead가 과도하지 않다.

## Atomic Instruction 기반 Lock

### Test-and-Set

Test-and-set은 memory 위치의 이전 값을 읽고 동시에 새 값을 쓴다. 이 연산이 atomic하면 두 thread가 동시에 lock을 획득하는 일을 막을 수 있다.

단순 spin lock은 구현이 쉽지만 lock이 풀릴 때까지 CPU를 계속 소모한다. Critical section이 짧고 thread 수가 적을 때만 적합하다.

### Compare-and-Swap

Compare-and-swap은 memory 값이 예상값과 같을 때만 새 값으로 바꾼다. Lock-free structure와 mutex 구현에 폭넓게 쓰인다.

### Fetch-and-Add

Fetch-and-add는 ticket lock 구현에 적합하다. 각 thread가 ticket을 받고, 현재 서비스 번호가 자신의 ticket과 같아질 때까지 기다린다. FIFO fairness를 제공할 수 있다.

### Load-Linked / Store-Conditional

LL/SC는 특정 주소를 load-linked한 뒤, 그 사이 다른 write가 없을 때만 store-conditional을 성공시킨다. RISC 계열에서 atomic update를 구현하는 방식이다.

## Condition Variable

Lock은 mutual exclusion을 제공하지만, 어떤 조건이 만족될 때까지 잠자는 기능은 따로 필요하다. Condition variable은 thread가 조건을 기다리도록 한다.

핵심 API:

- `pthread_cond_wait(&cond, &lock)`: lock을 release하고 sleep한 뒤, 깨어나면 lock을 다시 획득한다.
- `pthread_cond_signal(&cond)`: 기다리는 thread 하나를 깨운다.
- `pthread_cond_broadcast(&cond)`: 기다리는 모든 thread를 깨운다.

조건 대기는 반드시 `while`로 감싸야 한다.

```c
pthread_mutex_lock(&lock);
while (!condition)
    pthread_cond_wait(&cond, &lock);
// condition satisfied
pthread_mutex_unlock(&lock);
```

`if`가 아니라 `while`을 쓰는 이유는 spurious wakeup, 다른 thread가 먼저 조건을 소비하는 경우, broadcast 이후 재검사가 필요하기 때문이다.

## Producer-Consumer 문제

Producer-consumer는 bounded buffer를 공유하는 대표 synchronization 문제다.

- Buffer가 full이면 producer는 기다려야 한다.
- Buffer가 empty이면 consumer는 기다려야 한다.
- Buffer 접근 자체는 lock으로 보호해야 한다.

하나의 condition variable만 쓰면 잘못된 종류의 thread를 깨워 deadlock이나 비효율이 생길 수 있다. Producer용 조건과 consumer용 조건을 분리하면 더 명확하다.

## Semaphore

Semaphore는 정수 값과 두 연산으로 구성된다.

- `sem_wait()`: 값을 감소시키고, 감소할 수 없으면 block한다.
- `sem_post()`: 값을 증가시키고, 기다리는 thread를 깨울 수 있다.

초기값 1의 semaphore는 mutex처럼 사용할 수 있다. 초기값 0의 semaphore는 event ordering, 즉 child thread가 어떤 작업을 끝낼 때까지 parent가 기다리는 용도로 사용할 수 있다.

## Semaphore로 Producer-Consumer 구현

Bounded buffer에서는 보통 세 semaphore를 사용한다.

- `empty`: 빈 slot 개수
- `full`: 채워진 item 개수
- `mutex`: buffer 자료구조 보호

Producer는 `empty`를 먼저 기다린 뒤 `mutex`를 잡고 item을 넣고, `mutex`를 풀고 `full`을 증가시킨다. Consumer는 `full`을 먼저 기다린 뒤 `mutex`를 잡고 item을 꺼내고, `mutex`를 풀고 `empty`를 증가시킨다.

순서가 중요하다. `mutex`를 먼저 잡고 `empty`나 `full`을 기다리면, 조건을 바꿔야 할 반대편 thread가 buffer lock을 얻지 못해 deadlock이 생길 수 있다.

## Reader-Writer Lock

Reader-writer lock은 여러 reader가 동시에 critical section에 들어갈 수 있지만 writer는 단독으로 들어가게 한다. Read-mostly workload에서 concurrency를 높일 수 있다.

기본 아이디어:

- 첫 번째 reader가 writer lock을 획득해 writer를 막는다.
- 추가 reader들은 readers count만 증가시키고 함께 들어간다.
- 마지막 reader가 나가면서 writer lock을 해제한다.
- Writer는 writer lock을 단독으로 획득한다.

## 복습 체크포인트

- Thread가 process와 공유하는 자원과 독립적으로 가지는 자원을 구분할 수 있는가?
- `pthread_create`, `pthread_join`, `pthread_exit`의 역할을 설명할 수 있는가?
- Race condition을 instruction interleaving 관점에서 설명할 수 있는가?
- Lock, condition variable, semaphore의 역할 차이를 말할 수 있는가?
- Producer-consumer에서 `empty`, `full`, `mutex` semaphore의 순서가 왜 중요한가?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. 멀티스레딩**를 다루며, process, scheduling, virtual memory, concurrency, file system을 통해 OS가 hardware resource를 추상화하고 보호하는 방식을 이해한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

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
- **04. 멀티스레딩**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. 메모리 가상화 - 세그먼테이션과 페이징](03-memory-virtualization-segmentation-and-paging.md) · 다음: [05. IO와 디스크 드라이브](05-io-and-disk-drives.md)
