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

{% endraw %}

---

이전: [00. Introduction](00-introduction.md) · 다음: [02. Instructions Language of the Computer](02-instructions-language-of-the-computer.md)
