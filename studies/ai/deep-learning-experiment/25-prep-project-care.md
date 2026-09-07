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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **25. Project 사전보고서 CARE**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- 모델 구조를 읽을 때는 layer 이름보다 tensor shape, receptive field, skip connection, normalization 위치를 먼저 추적한다.
- loss가 직접 최적화하는 것과 metric이 평가하는 것을 분리해야 결과를 제대로 해석할 수 있다.
- 재현 실험은 seed, data split, augmentation, learning rate schedule, checkpoint 기준이 모두 결과에 영향을 준다.

### 문제 풀이 또는 구현 루틴

- 입력/출력 shape 표를 먼저 만든 뒤 forward pass를 작은 batch로 검증한다.
- 학습이 불안정하면 loss scale, gradient norm, learning rate, normalization, label 형식을 순서대로 확인한다.
- 보고서에는 baseline, ablation, failure case, 재현 가능한 hyperparameter를 함께 남긴다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 논문 그림만 보고 구현하면 padding, stride, channel order 같은 세부에서 쉽게 틀린다.
- validation 성능이 좋아도 data leakage가 있으면 의미가 없다.
- loss가 여러 개인 실험은 각 항의 scale을 따로 확인해야 한다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 모델의 inductive bias는 어떤 데이터 특성에 맞춰져 있는가?
- 성능 향상이 architecture 때문인지 training recipe 때문인지 어떻게 분리할 수 있는가?
- 결과 이미지만 보지 않고 어떤 수치와 failure case를 함께 확인해야 하는가?
- **25. Project 사전보고서 CARE**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [24. Lab13 사전보고서 RNN과 Seq2Seq](24-prep-lab13-rnn-seq2seq.md)
