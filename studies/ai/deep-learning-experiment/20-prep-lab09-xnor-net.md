---
layout: page
title: "20. Lab09 사전보고서 XNOR-Net과 양자화"
permalink: /studies/ai/deep-learning-experiment/20-prep-lab09-xnor-net/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/20%20Lab09%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20XNOR-Net%EA%B3%BC%20%EC%96%91%EC%9E%90%ED%99%94.md)

{% raw %}
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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **20. Lab09 사전보고서 XNOR-Net과 양자화**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
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
- **20. Lab09 사전보고서 XNOR-Net과 양자화**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [19. Lab08 사전보고서 CAM과 Grad-CAM](19-prep-lab08-cam-grad.md) · 다음: [21. Lab10 사전보고서 Style Transfer](21-prep-lab10-style-transfer.md)
