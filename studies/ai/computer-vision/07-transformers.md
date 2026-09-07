---
layout: page
title: "07. Transformers"
permalink: /studies/ai/computer-vision/07-transformers/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/07%20Transformers.md)

{% raw %}
## 한눈에 보기
Transformers 강의는 RNN의 순차 처리 한계를 self-attention으로 대체하는 구조를 설명하고, NLP에서 시작한 transformer가 vision으로 확장되는 흐름을 다룬다.

## 핵심 개념
- RNN
- self-attention
- scaled dot-product attention
- multi-head attention
- positional encoding
- encoder-decoder
- temperature
- Vision Transformer
- Swin Transformer

## 체계적 정리
- RNN은 sequence를 순서대로 처리하므로 긴 dependency와 병렬화에 한계가 있다.
- Transformer는 attention만으로 token 간 관계를 계산하고 recurrence와 convolution 없이 sequence를 처리한다.
- 입력 token에는 embedding과 positional encoding을 더해 순서 정보를 제공한다.
- scaled dot-product attention은 Query, Key, Value의 유사도를 softmax weight로 바꾸어 context를 만든다.
- multi-head attention은 여러 subspace에서 관계를 병렬로 학습한다.
- Vision Transformer는 image patch를 token처럼 다루고, Swin Transformer는 window attention으로 계산량을 줄인다.

## 중요한 수식과 관점
- Attention: $softmax(QK^T/\sqrt{d_k})V$.
- Temperature가 낮으면 distribution이 sharp해지고, 높으면 smooth해진다.
- self-attention complexity는 token 수에 대해 대략 $O(N^2)$이다.

## 구현과 학습 포인트
vision에서 transformer는 CNN의 local inductive bias를 줄이고 global dependency를 직접 학습한다. 대신 데이터와 계산량 요구가 커지므로 patch size, window size, pretraining의 영향이 크다.

## 자주 헷갈리는 지점
- positional encoding이 없으면 transformer는 token 순서를 직접 알기 어렵다.
- attention map이 높다고 항상 causal explanation을 의미하지는 않는다.
- ViT는 CNN보다 항상 좋은 것이 아니라 data scale과 training recipe에 민감하다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Detection and Segmentation](06-detection-and-segmentation.md)
- [Generative Models](08-generative-models.md)
- [기말고사 리뷰](99-final-exam-review.md)

{% endraw %}

---

이전: [06. Detection and Segmentation](06-detection-and-segmentation.md) · 다음: [08. Generative Models](08-generative-models.md)
