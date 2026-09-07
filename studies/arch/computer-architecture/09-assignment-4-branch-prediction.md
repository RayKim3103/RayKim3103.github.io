---
layout: page
title: "09. Assignment 4 Branch Prediction"
permalink: /studies/arch/computer-architecture/09-assignment-4-branch-prediction/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Architecture/lecture_notes/09%20Assignment%204%20Branch%20Prediction.md)

{% raw %}
## 한눈에 보기
Kite processor pipeline에 branch predictor와 branch target buffer를 구현하는 과제이다. fetch stage에서 branch 방향과 target을 예측하고, writeback stage에서 실제 결과를 바탕으로 predictor를 update한다.

## 핵심 개념
- branch predictor
- BTB
- two-bit saturating counter
- fetch stage
- execute stage
- writeback stage
- pipeline flush
- control hazard

## 체계적 정리
- fetch stage는 instruction memory에서 PC에 해당하는 instruction을 읽는다.
- fetched instruction이 SB type이면 branch predictor가 PC를 이용해 taken/not-taken을 예측한다.
- taken으로 예측되면 BTB에서 predicted target을 읽어 PC를 그 주소로 바꾸고, 아니면 PC+4로 진행한다.
- execute stage에서는 ALU가 실제 branch condition과 target을 계산한다.
- writeback stage에서는 실제 branch outcome으로 predictor를 update한다.
- branch가 taken이면 BTB도 실제 branch target으로 update한다.
- predicted target과 actual target이 다르면 pipeline register, ALU, register dependency map을 flush하고 PC를 correct target으로 바꾼다.
- branch predictor와 BTB는 branch instruction마다 fetch와 writeback에서 각각 접근된다.

## 중요한 수식과 관점
- Two-bit saturating counter는 taken이면 increment, not-taken이면 decrement하며 0과 3에서 saturate한다.
- beq target 예: condition true이면 PC + (imm << 1), false이면 PC + 4.
- Prediction accuracy = correct predictions / total branch predictions.

## 구현과 학습 포인트
과제 구현은 `br_predictor`와 `br_target_buffer`의 lookup/update 로직을 완성하는 작업으로 볼 수 있다. correctness는 잘못된 path instruction이 architectural state를 바꾸기 전에 flush되는지, predictor state가 실제 branch 결과로 업데이트되는지에 달려 있다.

## 자주 헷갈리는 지점
- branch direction과 branch target은 서로 다른 예측 문제다. 방향이 맞아도 target이 틀리면 flush가 필요하다.
- counter update를 fetch 시점에 하면 speculative path 때문에 predictor가 오염될 수 있다.
- flush할 때 IF/ID, ID/EX, EX/MEM, MEM/WB 등 pipeline state와 dependency map을 함께 정리해야 한다.
- BTB miss에서 taken으로 예측할 수 없거나 잘못된 target을 쓰면 PC 흐름이 깨진다.

## 복습 질문
- 이 자료에서 다루는 abstraction layer는 software 쪽에 가까운가, hardware 쪽에 가까운가?
- 성능을 판단할 때 clock rate, CPI, instruction count 중 무엇이 병목인지 어떻게 구분할 수 있는가?
- RISC-V 구현에서 register, memory, PC, immediate, pipeline state 중 어떤 값이 다음 단계로 전달되는가?

## 연결 노트
- [The Processor](04-the-processor.md)
- [Assignment 5 Cache](10-assignment-5-cache.md)
- [Computer Abstractions and Technology](01-computer-abstractions-and-technology.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **09. Assignment 4 Branch Prediction**를 다루며, ISA, datapath, control, memory hierarchy를 연결해 프로그램이 실제 하드웨어 위에서 실행되는 비용을 이해한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 컴퓨터구조 문제는 datapath에서 값이 흐르는 경로와 control signal이 켜지는 이유를 함께 그린다.
- 성능 계산은 cycle 수를 직접 세는 방식과 CPU time 식으로 검산하면 실수를 줄일 수 있다.
- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- ISA는 software와 hardware 사이의 계약이고, microarchitecture는 그 계약을 빠르게 실행하기 위한 구현 선택이다.

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
- **09. Assignment 4 Branch Prediction**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [08. Assignment 3 Floating-Point Numbers](08-assignment-3-floating-point-numbers.md) · 다음: [10. Assignment 5 Cache](10-assignment-5-cache.md)
