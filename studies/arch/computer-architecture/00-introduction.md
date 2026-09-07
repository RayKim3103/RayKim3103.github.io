---
layout: page
title: "00. Introduction"
permalink: /studies/arch/computer-architecture/00-introduction/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Architecture/lecture_notes/00%20Introduction.md)

{% raw %}
## 한눈에 보기
Computer Architecture 과목의 운영 방식과 수업 기대치를 소개하는 첫 자료이다. 본격적인 기술 내용보다 수업 등록, 출석, 과제, 시험, 질의응답 방식처럼 한 학기 학습을 진행하기 위한 규칙을 정리한다.

## 핵심 개념
- Computer Architecture
- course logistics
- Q&A
- attendance
- assignments
- exams
- RISC-V

## 체계적 정리
- EEE3530 Computer Architecture의 강의 개요와 담당 교수, 학교/학과 맥락을 확인한다.
- 수강 인원, registration period, attendance policy와 관련한 Q&A가 포함되어 있다.
- 학생은 정해진 범위 안에서 결석이 허용되지만, 관대한 출석 정책이 성적 관리와 무관하다는 점을 분리해서 이해해야 한다.
- 과제와 시험 성과가 최종 성적에 큰 영향을 주므로, 실습 환경과 RISC-V/Kite 사용법을 초반에 준비해야 한다.

## 중요한 수식과 관점
- 이 자료에는 핵심 수식보다 과목 운영 정보가 중심이다.
- 이후 강의에서 성능 식, ISA encoding, ALU, pipeline, cache 수식이 순차적으로 등장한다.

## 구현과 학습 포인트
첫 주 자료는 기술 구현보다 학습 환경을 정비하는 데 의미가 있다. 이후 과제는 Kite simulator와 RISC-V assembly를 사용하므로 Linux/Ubuntu 환경, shell command, make 기반 빌드 흐름을 미리 익혀두는 것이 좋다.

## 자주 헷갈리는 지점
- 출석 규정의 허용 범위를 과제나 시험 준비 여유로 오해하면 안 된다.
- Computer Architecture는 회로만 배우는 과목이 아니라 software와 hardware 사이의 interface를 함께 다룬다.
- 초반 환경 설정이 늦어지면 assembly, function call, pipeline 과제에서 시간이 많이 밀릴 수 있다.

## 복습 질문
- 이 자료에서 다루는 abstraction layer는 software 쪽에 가까운가, hardware 쪽에 가까운가?
- 성능을 판단할 때 clock rate, CPI, instruction count 중 무엇이 병목인지 어떻게 구분할 수 있는가?
- RISC-V 구현에서 register, memory, PC, immediate, pipeline state 중 어떤 값이 다음 단계로 전달되는가?

## 연결 노트
- [Computer Abstractions and Technology](01-computer-abstractions-and-technology.md)
- [Assignment 1 RISC-V Assembly](06-assignment-1-risc-v-assembly.md)
- [Instructions Language of the Computer](02-instructions-language-of-the-computer.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **00. Introduction**를 다루며, ISA, datapath, control, memory hierarchy를 연결해 프로그램이 실제 하드웨어 위에서 실행되는 비용을 이해한다.
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
- **00. Introduction**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

다음: [01. Computer Abstractions and Technology](01-computer-abstractions-and-technology.md)
