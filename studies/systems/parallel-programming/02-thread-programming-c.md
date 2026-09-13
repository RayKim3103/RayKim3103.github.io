---
layout: page
title: "02. Thread Programming - C++ Thread와 동기화"
permalink: /studies/systems/parallel-programming/02-thread-programming-c/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/02%20Thread%20Programming%20-%20C%2B%2B%20Thread%EC%99%80%20%EB%8F%99%EA%B8%B0%ED%99%94.md)

{% raw %}
이전: [Basic Parallel Architectures - 기본 병렬 아키텍처](01-basic-parallel-architectures.md)  
다음: [Matrix Multiplication - CPU Cache와 병렬 행렬곱](03-matrix-multiplication-cpu-cache.md)

## 핵심 요약

이 강의는 shared address space model에서 C++11 thread를 만들고, race condition을 막기 위한 mutex, lock guard, condition variable, atomic, barrier를 다룬다. 마지막에는 Amdahl's Law를 통해 병렬화 가능한 부분이 전체 speedup을 제한한다는 점을 정리한다.

## Programming Model

| 모델 | 통신 추상화 | 특징 |
|---|---|---|
| Shared memory | 같은 주소 공간을 read/write | thread 간 데이터 공유가 쉽지만 race 발생 |
| Message passing | message send/receive | 분산 메모리에서 확장성 좋음 |

Shared address space에서는 모든 thread가 같은 address를 보면 같은 값을 본다. 단, cache hierarchy와 NUMA 때문에 실제 latency는 균일하지 않을 수 있다.

## Memory Hierarchy와 NUMA

현대 CPU는 L1/L2/L3 cache와 memory hierarchy를 가진다. dual socket machine에서는 local memory와 remote memory 접근 latency가 다를 수 있다. 이 구조를 NUMA라고 한다.

NUMA에서 중요한 점:

- 주소 공간은 공유되지만 물리적 위치에 따라 접근 시간이 다르다.
- remote memory 접근은 local memory보다 느릴 수 있다.
- thread 배치와 memory allocation 위치가 성능에 영향을 준다.

## C++ Thread

`std::thread`는 callable을 받아 새 thread를 시작한다.

Callable 종류:

- function pointer
- function object
- lambda expression

`join()`은 child thread가 끝날 때까지 caller가 기다리는 동작이고, `detach()`는 thread를 독립 실행시킨다. join하지 않을 thread는 detach해야 resource leak이나 program termination 문제를 피할 수 있다.

## Race Condition

`count++`는 실제로 read, add, write의 여러 단계로 나뉜다. 두 thread가 동시에 같은 값을 읽고 쓰면 실행 순서에 따라 결과가 달라진다. 이것이 race condition이다.

## Mutex와 Critical Section

Mutex는 lock/unlock 상태를 가진 동기화 변수다. 한 thread만 lock을 잡고 critical section에 들어갈 수 있다.

```cpp
std::mutex global_mutex;

void inc(int* output) {
    global_mutex.lock();
    (*output)++;
    global_mutex.unlock();
}
```

직접 lock/unlock을 쓰면 return, exception, branch 때문에 unlock을 놓쳐 deadlock이 생길 수 있다.

## RAII와 `lock_guard`

RAII는 resource 획득과 해제를 object lifetime에 묶는 C++ 패턴이다. `std::lock_guard`는 생성 시 lock하고 scope를 벗어나면 자동 unlock한다.

```cpp
void inc(int* output) {
    std::lock_guard<std::mutex> lock(global_mutex);
    (*output)++;
}
```

Thread join도 `thread_guard` 같은 RAII object로 감싸면 예외 상황에서도 join 누락을 줄일 수 있다.

## Deadlock

두 mutex를 서로 다른 순서로 잡으면 thread들이 서로를 기다리며 멈출 수 있다. 해결책은 항상 같은 순서로 lock하거나, `std::lock()`으로 여러 mutex를 deadlock 없이 동시에 lock하는 것이다.

`std::adopt_lock`은 이미 lock된 mutex를 `lock_guard`가 관리하게 할 때 사용한다.

## Condition Variable

Producer-consumer queue에서 busy wait은 CPU를 낭비한다. sleep을 넣은 busy wait도 latency와 낭비가 남는다. 더 좋은 방식은 condition variable이다.

```cpp
cond.wait(lock, [] { return !shared_queue.empty(); });
```

조건이 만족될 때까지 thread를 재우고, producer가 `notify`하면 깨어나므로 효율적이다.

## Atomic과 Barrier

- `std::atomic`: critical section이 아주 짧은 단순 연산에 유리하다.
- Barrier: 여러 thread가 특정 지점에 모두 도착할 때까지 기다리는 global synchronization이다.

C++ 표준 지원이 부족한 시점에는 Boost barrier 같은 library를 사용할 수 있다.

## Thread-Safe Data Structure

자료구조가 shared data를 조작하면서도 multi-thread 실행에 안전하면 thread-safe하다고 한다. 단순 방법은 전체 자료구조를 하나의 mutex로 보호하는 것이지만, 성능은 낮다. linked list는 fine-grained locking을 고려할 수 있으나 node-by-node lock은 제거/삽입에서 까다롭고, hand-over-hand locking이 필요하다.

## Amdahl's Law

전체 실행 시간이 serial part `S`와 parallel part `P`로 나뉜다면, processor 수를 늘려도 serial part가 speedup의 상한을 만든다.

핵심 메시지:

- 병렬화 가능한 비율이 작으면 core 수를 늘려도 효과가 제한된다.
- atomic, job division, synchronization overhead도 serial part처럼 작동할 수 있다.
- 병렬 프로그래밍에서는 “얼마나 많이 나누는가”만큼 “나눌 수 없는 부분을 줄이는가”가 중요하다.

## 숫자로 확인하기 — Amdahl's Law로 speedup 상한 계산

프로그램의 90%($$P=0.9$$)가 병렬화 가능하고 10%($$S=0.1$$)가 순수 serial이라고 하자. Processor 수 $$N$$에 대한 speedup은

$$
\text{Speedup}(N) = \frac{1}{S + \frac{P}{N}}
$$

| $$N$$ | 계산 | Speedup |
|---:|---|---:|
| 1 | $$1/(0.1+0.9/1)$$ | 1.00 |
| 4 | $$1/(0.1+0.9/4)$$ | 3.08 |
| 16 | $$1/(0.1+0.9/16)$$ | 6.40 |
| 64 | $$1/(0.1+0.9/64)$$ | 8.77 |
| $$\infty$$ | $$1/S = 1/0.1$$ | **10.0** |

Processor를 4개에서 16개로 4배 늘리면 speedup은 3.08→6.40(약 2.1배)로 늘지만, 16개에서 64개로 또 4배 늘려도 6.40→8.77(약 1.4배)로 개선 폭이 급격히 줄어든다. Processor를 아무리 늘려도 speedup은 $$1/S=10$$을 절대 넘지 못한다 — serial 10%가 이 프로그램이 낼 수 있는 최대 speedup을 영원히 10배로 묶어버린다. Mutex 경합, thread 생성/조인 overhead, barrier 대기 같은 synchronization 비용은 모두 이 $$S$$에 더해지는 "실질적인 serial part"로 작동하므로, 병렬 코드를 늘리는 것 못지않게 이 synchronization overhead를 줄이는 것이 중요하다.

## 복습 질문

- 병렬 비율 90%에서 processor 수를 4→16→64로 늘릴 때 speedup 증가폭이 왜 점점 줄어드는지 설명할 수 있는가?
- 이 예제에서 processor를 무한히 늘려도 speedup이 10을 넘을 수 없는 이유를 수식으로 설명할 수 있는가?
- Mutex 경합이나 barrier 대기 같은 synchronization overhead가 왜 Amdahl's Law의 serial part $$S$$에 더해지는 비용으로 볼 수 있는지 설명할 수 있는가?

## 정리

Thread programming의 핵심은 shared data를 다루는 순간 correctness와 performance가 동시에 어려워진다는 점이다. mutex는 안전하지만 과하면 느리고, atomic은 가볍지만 적용 범위가 좁고, condition variable은 대기 문제를 효율적으로 해결한다. 마지막으로 Amdahl's Law는 동기화와 serial section을 줄이는 것이 성능의 본질임을 보여준다.

{% endraw %}

---

이전: [01. Basic Parallel Architectures - 기본 병렬 아키텍처](01-basic-parallel-architectures.md) · 다음: [03. Matrix Multiplication - CPU Cache와 병렬 행렬곱](03-matrix-multiplication-cpu-cache.md)
