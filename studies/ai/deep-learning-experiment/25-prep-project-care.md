---
layout: page
title: "25. Project 사전보고서 CARE"
permalink: /studies/ai/deep-learning-experiment/25-prep-project-care/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/25%20Project%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20CARE.md)

{% raw %}
## 한눈에 보기
CARE 프로젝트의 문제 정의와 관련 연구를 정리한 사전보고서이다.

## 핵심 개념
- long-tailed distribution
- class imbalance
- cRT
- class-balanced loss
- Balanced Softmax
- LDAM
- logit adjustment
- multi-expert

## 이론 정리
- long-tailed dataset에서는 소수 head class에 sample이 집중되고 tail class는 representation과 classifier boundary가 모두 불안정해진다.
- class-balanced loss는 effective number of samples로 rare class에 더 큰 가중을 준다.
- cRT는 backbone representation을 먼저 학습한 뒤 classifier만 class-balanced 방식으로 재학습한다.
- Balanced Softmax는 training class prior를 softmax denominator에 반영해 prior shift를 보정한다.
- LDAM은 class별 margin을 다르게 두어 minority class에 더 큰 decision margin을 부여한다.
- multi-expert 방식은 서로 다른 objective를 가진 classifier를 결합해 head/medium/tail trade-off를 완화한다.

## 중요한 수식과 관점
- Long-tail split: $n_c=450-4c$, class 0은 450장, class 99는 54장.
- Logit adjustment는 class prior의 log를 logit에 더하거나 빼서 prior bias를 보정한다.

## 실습과의 연결
프로젝트 구현에서는 Stage 1 regularized ResNet-18 표현 학습 후, Stage 2 CARE expert들이 class-balanced, Balanced Softmax, LDAM/class-center 목적을 나누어 담당한다.

## 추가 해석 포인트
- long-tailed recognition은 단순히 sample 수가 적은 class의 문제가 아니라 representation learning, classifier prior, calibration이 얽힌 문제다.
- cRT가 효과적인 이유는 representation은 전체 데이터로 학습하고, classifier decision boundary만 balanced sampling으로 다시 맞추기 때문이다.
- Balanced Softmax와 logit adjustment는 train/test prior 차이를 수식적으로 보정하는 방법으로 볼 수 있다.
- CARE의 multi-expert 설계는 하나의 objective가 head와 tail을 동시에 최적화하기 어렵다는 관찰에서 출발한다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Project CARE 장기꼬리 CIFAR-100 구현](13-project-care-cifar-100.md)
- [Lab09 사전보고서 XNOR-Net과 양자화](20-prep-lab09-xnor-net.md)
- [Lab13 사전보고서 RNN과 Seq2Seq](24-prep-lab13-rnn-seq2seq.md)

{% endraw %}

---

이전: [24. Lab13 사전보고서 RNN과 Seq2Seq](24-prep-lab13-rnn-seq2seq.md)
