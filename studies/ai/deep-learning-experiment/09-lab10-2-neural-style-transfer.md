---
layout: page
title: "09. Lab10-2 Neural Style Transfer 반복 실험"
permalink: /studies/ai/deep-learning-experiment/09-lab10-2-neural-style-transfer/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/09%20Lab10-2%20Neural%20Style%20Transfer%20%EB%B0%98%EB%B3%B5%20%EC%8B%A4%ED%97%98.md)

{% raw %}
## 한눈에 보기
Lab10의 style transfer 구현을 바탕으로 initialization, alpha/beta 비율, content layer 위치, style image를 바꿔가며 결과 차이를 분석한 반복 실험 노트이다.

## 핵심 개념
- hyperparameter ablation
- initialization
- content/style balance
- content layer depth
- Picasso style
- mosaic style

## 실습 흐름
- content image 초기화, white-noise 초기화, style-image 초기화를 비교한다.
- mosaic style과 Picasso style 등 서로 다른 style target을 사용한다.
- alpha를 1e-1, 1e1처럼 조정해 content 보존 강도를 관찰한다.
- beta를 1e3~1e7 범위로 조정해 style 강도를 관찰한다.
- content layer를 conv2-2, conv3-4, conv4-3 등으로 바꿔 feature depth에 따른 결과를 비교한다.

## 구현과 이론의 연결
- content initialization은 빠르고 구조 보존이 강하다. white-noise initialization은 자유도가 높아 style texture가 강하지만 수렴이 느리다. style-image initialization은 색감과 texture가 빠르게 반영되지만 content layout 복원이 어려울 수 있다.
- alpha가 커지면 content feature reconstruction이 우세해 원본 구조가 유지된다. beta가 커지면 Gram matrix matching이 우세해 붓질과 색 분포가 강해진다.
- content layer가 shallow하면 pixel/edge 정합이 강하고, deep하면 semantic object layout을 중심으로 보존된다.

## 결과와 해석
- 여러 case에서 loss가 반복과 함께 감소했고, 예시 로그는 50~500 iteration 구간에서 꾸준히 줄어드는 형태를 보였다.
- style weight가 과도하게 클 때는 style texture가 화면 전체를 압도해 content 식별성이 낮아졌다.
- deep content layer를 사용할수록 작은 edge보다 큰 구조가 유지되어 style과 content의 융합이 자연스러워지는 경향을 보였다.

## 자주 헷갈리는 지점
- 실험 간 비교는 같은 content/style image, 같은 random seed, 같은 iteration 수를 유지해야 공정하다.
- style loss 값은 layer 수와 normalization에 따라 scale이 달라지므로 alpha/beta 절대값만으로 해석하면 위험하다.
- 결과 이미지는 정성 평가가 중요하지만, loss curve와 함께 보아야 수렴 실패와 취향 차이를 구분할 수 있다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab10 Neural Style Transfer](08-lab10-neural-style-transfer.md)
- [Lab10 사전보고서 Style Transfer](21-prep-lab10-style-transfer.md)
- [Lab12 CycleGAN](11-lab12-cyclegan.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **09. Lab10-2 Neural Style Transfer 반복 실험**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 신경망 주제에서는 activation shape, parameter count, gradient path, normalization 위치를 함께 추적한다.
- 깊은 모델의 성능은 architecture뿐 아니라 initialization, optimizer, learning rate schedule, augmentation에 민감하다.
- 생성모델은 likelihood, adversarial objective, reconstruction/perceptual loss가 각각 다른 품질 기준을 최적화한다.
- 샘플 이미지는 mode collapse, blur, texture artifact, conditioning failure를 따로 보며 해석한다.
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
- **09. Lab10-2 Neural Style Transfer 반복 실험**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [08. Lab10 Neural Style Transfer](08-lab10-neural-style-transfer.md) · 다음: [10. Lab11 DCGAN](10-lab11-dcgan.md)
