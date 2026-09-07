---
layout: page
title: "24. Lab13 사전보고서 RNN과 Seq2Seq"
permalink: /studies/ai/deep-learning-experiment/24-prep-lab13-rnn-seq2seq/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/24%20Lab13%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20RNN%EA%B3%BC%20Seq2Seq.md)

{% raw %}
## 한눈에 보기
RNN 계열 모델과 attention 기반 sequence-to-sequence 번역 모델을 정리한 사전보고서이다.

## 핵심 개념
- vanilla RNN
- GRU
- LSTM
- long-term dependency
- Seq2Seq
- attention
- alignment
- BLEU

## 이론 정리
- vanilla RNN은 이전 hidden state와 현재 입력을 반복적으로 결합하지만 gradient vanishing/exploding으로 long-term dependency에 약하다.
- LSTM은 input/forget/output gate와 cell state로 장기 기억을 조절한다.
- GRU는 reset/update gate로 LSTM보다 단순한 구조를 유지하면서 장기 의존성을 완화한다.
- Seq2Seq encoder-decoder는 source sentence를 context로 압축하고 decoder가 target sentence를 생성한다.
- Bahdanau attention은 decoder state와 encoder outputs 사이 alignment score를 계산하여 매 decoding step마다 필요한 source 위치를 참조한다.

## 중요한 수식과 관점
- Attention context: $c_t=\sum_i \alpha_{t,i}h_i$.
- Alignment weight: $\alpha_{t,i}=softmax(score(s_{t-1},h_i))$.

## 실습과의 연결
Lab13 구현은 vocabulary 구축, GRU encoder, attention decoder, teacher forcing training, attention map visualization으로 이어진다.

## 추가 해석 포인트
- encoder-decoder의 bottleneck은 source sentence 길이가 길수록 심해진다. Attention은 모든 encoder state를 decoder가 동적으로 다시 보게 하여 이를 완화한다.
- teacher forcing은 학습을 빠르게 하지만 train/test mismatch를 만든다. scheduled sampling 같은 변형은 이 문제를 줄이려는 시도다.
- BLEU는 n-gram overlap 기반 metric이라 번역 품질의 일부만 측정한다. attention map과 실제 예문을 함께 봐야 한다.
- Lab13의 attention visualization은 모델이 alignment를 학습했는지 확인하는 중요한 질적 검증이다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab13 Attention 기반 기계번역](12-lab13-attention.md)
- [Project 사전보고서 CARE](25-prep-project-care.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **24. Lab13 사전보고서 RNN과 Seq2Seq**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
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
- **24. Lab13 사전보고서 RNN과 Seq2Seq**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [23. Lab12 사전보고서 Pix2Pix와 CycleGAN](23-prep-lab12-pix2pix-cyclegan.md) · 다음: [25. Project 사전보고서 CARE](25-prep-project-care.md)
