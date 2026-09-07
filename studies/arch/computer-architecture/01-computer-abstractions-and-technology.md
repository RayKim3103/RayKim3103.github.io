---
layout: page
title: "01. Computer Abstractions and Technology"
permalink: /studies/arch/computer-architecture/01-computer-abstractions-and-technology/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Architecture/lecture_notes/01%20Computer%20Abstractions%20and%20Technology.md)

{% raw %}
## 한눈에 보기
컴퓨터 시스템을 application에서 transistor까지 여러 abstraction layer로 나누어 보고, 성능과 전력/에너지 효율을 어떻게 측정하는지 설명하는 자료이다. 컴퓨터 구조가 software와 hardware 사이에서 어떤 역할을 하는지 잡아준다.

## 핵심 개념
- abstraction layers
- ISA
- microarchitecture
- performance
- throughput
- response time
- power
- energy
- Amdahl's law

## 체계적 정리
- 컴퓨터 설계는 application, algorithm, programming language, compiler, operating system, ISA, microarchitecture, RTL, circuit, device, material layer로 나뉜다.
- ISA는 software가 hardware를 사용하는 약속이며, assembly instruction은 이 interface의 구체적 표현이다.
- computing system은 desktop/server/mobile/embedded/supercomputer처럼 목표 workload와 제약이 다르다.
- 성능은 response time과 throughput으로 나눠 볼 수 있다. 개인 사용자는 latency를, server 운영자는 처리량과 에너지 비용을 크게 본다.
- 전력은 순간 소비율이고 에너지는 일정 시간 동안 소비한 총량이다. 데이터센터와 모바일 시스템에서는 성능뿐 아니라 performance per watt가 중요하다.
- Amdahl's law는 일부만 개선했을 때 전체 성능 향상이 제한된다는 점을 보여준다.

## 중요한 수식과 관점
- CPU time = Instruction Count x CPI x Clock Cycle Time.
- CPU time = Instruction Count x CPI / Clock Rate.
- Power = Energy / Time.
- Amdahl's law: Speedup = 1 / ((1 - f) + f / s).

## 구현과 학습 포인트
프로그램 성능을 높이려면 단순히 clock rate만 올리는 것이 아니라 instruction count, CPI, memory stall, branch stall, compiler output을 함께 봐야 한다. 뒤쪽 pipeline과 cache 강의는 이 성능 식의 CPI와 stall 항을 줄이는 방법으로 연결된다.

## 자주 헷갈리는 지점
- clock rate가 높아도 CPI나 instruction count가 나빠지면 프로그램이 빨라지지 않을 수 있다.
- throughput 향상과 response time 향상은 같은 말이 아니다.
- Amdahl's law에서 개선되지 않는 부분이 전체 speedup의 상한을 결정한다.
- power와 energy를 혼동하면 모바일/서버 시스템의 효율 평가가 틀어진다.

## 복습 질문
- 이 자료에서 다루는 abstraction layer는 software 쪽에 가까운가, hardware 쪽에 가까운가?
- 성능을 판단할 때 clock rate, CPI, instruction count 중 무엇이 병목인지 어떻게 구분할 수 있는가?
- RISC-V 구현에서 register, memory, PC, immediate, pipeline state 중 어떤 값이 다음 단계로 전달되는가?

## 연결 노트
- [Instructions Language of the Computer](02-instructions-language-of-the-computer.md)
- [The Processor](04-the-processor.md)
- [Memory Hierarchy](05-memory-hierarchy.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **01. Computer Abstractions and Technology**를 다루며, ISA, datapath, control, memory hierarchy를 연결해 프로그램이 실제 하드웨어 위에서 실행되는 비용을 이해한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- ISA는 software와 hardware 사이의 계약이고, microarchitecture는 그 계약을 빠르게 실행하기 위한 구현 선택이다.
- 성능은 instruction count, CPI, clock period의 곱으로 나누어 보아야 병목이 명확해진다.
- pipeline, cache, branch prediction은 평균 성능을 높이지만 hazard, miss, misprediction이라는 예외 비용을 만든다.

### 문제 풀이 또는 구현 루틴

- 명령어를 보면 operand 위치, control signal, datapath 경로, writeback 대상을 순서대로 추적한다.
- 성능 문제는 baseline cycle을 세고 stall/miss penalty를 더해 CPI로 환산한다.
- assembly 과제에서는 calling convention, stack frame, register 보존 규칙을 표로 확인한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- clock frequency만 보고 성능을 판단하면 CPI와 instruction count 효과를 놓친다.
- pipeline은 개별 명령어 latency를 크게 줄이기보다 throughput을 높이는 기법이다.
- cache hit rate가 높아도 miss penalty가 크면 전체 실행 시간이 크게 늘 수 있다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 주제는 ISA 수준 설명인가, microarchitecture 구현인가?
- 병목이 계산, 메모리, branch, synchronization 중 어디에 있는가?
- 같은 프로그램을 더 빠르게 만들 때 compiler, ISA, hardware 중 어느 층을 바꿀 수 있는가?
- **01. Computer Abstractions and Technology**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [00. Introduction](00-introduction.md) · 다음: [02. Instructions Language of the Computer](02-instructions-language-of-the-computer.md)
