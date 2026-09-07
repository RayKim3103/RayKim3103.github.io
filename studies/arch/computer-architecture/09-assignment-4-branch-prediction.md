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

{% endraw %}

---

이전: [08. Assignment 3 Floating-Point Numbers](08-assignment-3-floating-point-numbers.md) · 다음: [10. Assignment 5 Cache](10-assignment-5-cache.md)
