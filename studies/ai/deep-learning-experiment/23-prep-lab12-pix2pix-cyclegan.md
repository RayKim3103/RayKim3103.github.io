---
layout: page
title: "23. Lab12 사전보고서 Pix2Pix와 CycleGAN"
permalink: /studies/ai/deep-learning-experiment/23-prep-lab12-pix2pix-cyclegan/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/23%20Lab12%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20Pix2Pix%EC%99%80%20CycleGAN.md)

{% raw %}
## 한눈에 보기
paired image-to-image translation인 Pix2Pix와 unpaired translation인 CycleGAN을 비교한 사전보고서이다.

## 핵심 개념
- conditional GAN
- Pix2Pix
- paired data
- CycleGAN
- unpaired data
- cycle consistency
- PatchGAN

## 이론 정리
- Pix2Pix는 input image와 target image가 쌍으로 존재하므로 cGAN loss와 L1 reconstruction loss를 함께 사용한다.
- L1 loss는 L2보다 blur가 덜하고 구조 보존에 유리하다.
- CycleGAN은 paired target이 없기 때문에 adversarial loss만으로는 input content가 보존되지 않는다.
- cycle consistency loss는 X->Y->X, Y->X->Y 복원을 강제하여 content preservation을 제공한다.
- image buffer와 least-squares GAN loss 같은 안정화 기법은 oscillation을 줄이는 데 도움을 준다.

## 중요한 수식과 관점
- Pix2Pix objective: $L_{cGAN}(G,D)+\lambda L_1(G)$.
- Cycle loss: $E_x\|F(G(x))-x\|_1 + E_y\|G(F(y))-y\|_1$.

## 실습과의 연결
Lab12에서는 MNIST와 SVHN을 unpaired domain으로 두고 두 generator, 두 discriminator, GAN loss, cycle L1 loss, linear decay scheduler를 구현했다.

## 추가 해석 포인트
- Pix2Pix는 paired supervision 덕분에 mapping이 명확하지만, paired dataset 구축 비용이 크다.
- CycleGAN은 unpaired data만으로 학습 가능해 적용 범위가 넓지만, cycle consistency가 semantic correctness를 완벽히 보장하지는 않는다.
- PatchGAN discriminator는 전체 이미지 하나의 score보다 local texture realism을 강하게 평가한다.
- MNIST-SVHN 변환에서는 digit identity를 보존하면서 domain style만 바꾸는지가 핵심 qualitative criterion이다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab12 CycleGAN](11-lab12-cyclegan.md)
- [Lab11 사전보고서 GAN과 DCGAN](22-prep-lab11-gan-dcgan.md)
- [Lab10 사전보고서 Style Transfer](21-prep-lab10-style-transfer.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **23. Lab12 사전보고서 Pix2Pix와 CycleGAN**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
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
- **23. Lab12 사전보고서 Pix2Pix와 CycleGAN**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [22. Lab11 사전보고서 GAN과 DCGAN](22-prep-lab11-gan-dcgan.md) · 다음: [24. Lab13 사전보고서 RNN과 Seq2Seq](24-prep-lab13-rnn-seq2seq.md)
