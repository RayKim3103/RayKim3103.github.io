---
layout: page
title: "21. Lab10 사전보고서 Style Transfer"
permalink: /studies/ai/deep-learning-experiment/21-prep-lab10-style-transfer/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/21%20Lab10%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20Style%20Transfer.md)

{% raw %}
## 한눈에 보기
Neural Style Transfer와 perceptual loss 기반 image transformation의 배경을 정리한 사전보고서이다.

## 핵심 개념
- Gatys style transfer
- perceptual loss
- VGG feature
- Gram matrix
- image transform network
- total variation

## 이론 정리
- Gatys 방식은 output image 자체를 최적화하여 content feature와 style Gram matrix를 동시에 맞춘다.
- style은 특정 layer feature channel 간 상관관계인 Gram matrix로 표현된다.
- Johnson 방식은 transform network를 미리 학습하여 test time에는 한 번의 forward pass로 style transfer를 수행한다.
- feature reconstruction loss는 semantic content를, style reconstruction loss는 texture와 color statistics를 반영한다.
- total variation regularization은 output image의 spatial smoothness를 높인다.

## 중요한 수식과 관점
- Total loss: $\alpha L_{content}+\beta L_{style}+\gamma L_{tv}$.
- Gram matrix: $G_{ij}=\sum_k F_{ik}F_{jk}$.

## 실습과의 연결
Lab10에서는 VGG loss network를 고정하고 optimization 기반 방법과 feed-forward ImageTransformNet을 모두 구현해 속도와 품질 차이를 비교했다.

## 추가 해석 포인트
- content와 style은 엄밀한 물리량이 아니라 pretrained recognition network가 학습한 representation에서 정의된 개념이다.
- Gram matrix는 spatial 위치를 지우고 channel correlation을 남기므로 texture 통계에는 강하지만 구조 정보는 약하다.
- perceptual loss는 pixel-wise 정답이 하나로 정해지기 어려운 image generation 문제에서 blur를 줄이는 데 유리하다.
- Lab10의 alpha, beta 조정은 content 보존과 style 강도 사이의 직접적인 trade-off를 관찰하는 실험이다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab10 Neural Style Transfer](08-lab10-neural-style-transfer.md)
- [Lab10-2 Neural Style Transfer 반복 실험](09-lab10-2-neural-style-transfer.md)
- [Lab05 사전보고서 FSRCNN](16-prep-lab05-fsrcnn.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **21. Lab10 사전보고서 Style Transfer**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 생성모델은 likelihood, adversarial objective, reconstruction/perceptual loss가 각각 다른 품질 기준을 최적화한다.
- 샘플 이미지는 mode collapse, blur, texture artifact, conditioning failure를 따로 보며 해석한다.
- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- 모델 구조를 읽을 때는 layer 이름보다 tensor shape, receptive field, skip connection, normalization 위치를 먼저 추적한다.

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
- **21. Lab10 사전보고서 Style Transfer**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [20. Lab09 사전보고서 XNOR-Net과 양자화](20-prep-lab09-xnor-net.md) · 다음: [22. Lab11 사전보고서 GAN과 DCGAN](22-prep-lab11-gan-dcgan.md)
