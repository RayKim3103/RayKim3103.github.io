---
layout: page
title: "24. Lab13 사전보고서 RNN과 Seq2Seq"
permalink: /studies/ai/deep-learning-experiment/24-prep-lab13-rnn-seq2seq/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/24%20Lab13%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20RNN%EA%B3%BC%20Seq2Seq.md)

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


---

이전: [23. Lab12 사전보고서 Pix2Pix와 CycleGAN](23-prep-lab12-pix2pix-cyclegan.md) · 다음: [25. Project 사전보고서 CARE](25-prep-project-care.md)
