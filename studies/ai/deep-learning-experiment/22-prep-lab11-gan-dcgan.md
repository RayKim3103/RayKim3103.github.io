---
layout: page
title: "22. Lab11 사전보고서 GAN과 DCGAN"
permalink: /studies/ai/deep-learning-experiment/22-prep-lab11-gan-dcgan/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/22%20Lab11%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20GAN%EA%B3%BC%20DCGAN.md)

{% raw %}
## 한눈에 보기
GAN의 minimax 학습 원리와 DCGAN의 안정적 convolutional architecture를 정리한 사전보고서이다.

## 핵심 개념
- GAN
- minimax game
- generator
- discriminator
- mode collapse
- DCGAN architecture
- batch normalization

## 이론 정리
- GAN은 명시적 likelihood 계산 없이 adversarial learning으로 생성 모델을 학습한다.
- 이론적으로 최적 discriminator 아래에서 generator는 data distribution에 가까워지는 방향으로 학습된다.
- 원래 generator objective는 saturation 문제가 있어 실제 구현에서는 $\log D(G(z))$를 키우는 non-saturating loss를 자주 쓴다.
- DCGAN은 strided convolution, transposed convolution, BatchNorm, ReLU/LeakyReLU 조합으로 GAN 학습 안정성을 높였다.

## 중요한 수식과 관점
- Minimax objective: $\min_G\max_D E_{x\sim p_{data}}[\log D(x)] + E_{z\sim p_z}[\log(1-D(G(z)))]$.
- Generator non-saturating objective: $\max_G E_z[\log D(G(z))]$.

## 실습과의 연결
Lab11에서는 BCE loss, alternating update, Generator/Discriminator convolution block, 생성 이미지 주기적 시각화를 구현했다.

## 추가 해석 포인트
- GAN은 생성 분포와 실제 분포의 거리를 직접 수식으로 최소화하기보다 discriminator가 제공하는 학습 신호를 이용한다.
- mode collapse는 generator가 다양한 sample 대신 discriminator를 속이기 쉬운 일부 mode만 생성하는 현상이다.
- DCGAN의 BatchNorm은 activation scale을 안정화하지만, Discriminator 마지막 layer와 Generator output layer에는 보통 넣지 않는다.
- GAN 실험에서는 loss curve만으로 품질을 판단하기 어렵기 때문에 생성 이미지 grid를 주기적으로 확인해야 한다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab11 DCGAN](10-lab11-dcgan.md)
- [Lab12 사전보고서 Pix2Pix와 CycleGAN](23-prep-lab12-pix2pix-cyclegan.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **22. Lab11 사전보고서 GAN과 DCGAN**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
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
- **22. Lab11 사전보고서 GAN과 DCGAN**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [21. Lab10 사전보고서 Style Transfer](21-prep-lab10-style-transfer.md) · 다음: [23. Lab12 사전보고서 Pix2Pix와 CycleGAN](23-prep-lab12-pix2pix-cyclegan.md)
