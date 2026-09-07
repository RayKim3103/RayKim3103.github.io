---
layout: page
title: "20. Lab09 사전보고서 XNOR-Net과 양자화"
permalink: /studies/ai/deep-learning-experiment/20-prep-lab09-xnor-net/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/20%20Lab09%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20XNOR-Net%EA%B3%BC%20%EC%96%91%EC%9E%90%ED%99%94.md)

## 한눈에 보기
binary/low-bit neural network의 동기와 대표 방법을 정리한 사전보고서이다.

## 핵심 개념
- binary neural network
- XNOR-Net
- BWN
- activation quantization
- STE
- EWGS
- QIL

## 이론 정리
- BWN은 weight만 binary로 만들고 scaling factor를 곱해 full precision convolution을 근사한다.
- XNOR-Net은 weight와 activation을 모두 binary화하여 convolution을 XNOR와 bitcount 연산으로 대체할 수 있다.
- 입력 첫 layer나 출력 classifier까지 모두 binary화하면 정보 손실이 커질 수 있어 일부 layer는 full precision으로 남긴다.
- STE는 rounding/sign function의 non-differentiability를 우회하는 기본 도구다.
- EWGS와 QIL 같은 방법은 gradient scaling 또는 learnable interval을 통해 quantized model의 trainability를 높인다.

## 중요한 수식과 관점
- Binary weight 근사: $W \approx \alpha B$, $B\in\{-1,+1\}$.
- Quantization은 clipping, scaling, rounding, de-scaling 단계로 볼 수 있다.

## 실습과의 연결
Lab09에서는 QConv2d, weight/activation quantizer, custom rounding, pretrained initialization을 구현해 full-precision accuracy에 가까운 quantized accuracy를 회복했다.

## 추가 해석 포인트
- binary network는 multiplication을 bit operation으로 바꿀 수 있어 hardware deployment에서 특히 매력적이다.
- 하지만 activation까지 binary화하면 표현 가능한 함수 공간이 급격히 줄어 accuracy 손실이 커질 수 있다.
- scaling factor는 binary approximation의 크기 정보를 보완해 full precision tensor와의 차이를 줄인다.
- QIL처럼 interval을 학습하는 방식은 어떤 값을 보존하고 어떤 값을 saturate할지 모델이 data에 맞춰 결정하게 해준다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab09 Quantization Interval Learning](07-lab09-quantization-interval-learning.md)
- [Project 사전보고서 CARE](25-prep-project-care.md)


---

이전: [19. Lab08 사전보고서 CAM과 Grad-CAM](19-prep-lab08-cam-grad.md) · 다음: [21. Lab10 사전보고서 Style Transfer](21-prep-lab10-style-transfer.md)
