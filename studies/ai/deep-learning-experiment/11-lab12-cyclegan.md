---
layout: page
title: "11. Lab12 CycleGAN"
permalink: /studies/ai/deep-learning-experiment/11-lab12-cyclegan/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/11%20Lab12%20CycleGAN.md)

{% raw %}
## 한눈에 보기
paired ground truth 없이 두 도메인 사이의 image translation을 학습하는 CycleGAN 실습이다. MNIST와 SVHN처럼 서로 대응쌍이 없는 데이터에서 X->Y, Y->X generator와 각 domain discriminator를 동시에 학습한다.

## 핵심 개념
- CycleGAN
- unpaired image translation
- MNIST to SVHN
- cycle consistency
- GAN loss
- L1 loss
- linear learning rate decay

## 실습 흐름
- MNIST는 1-channel 32x32, SVHN은 3-channel 32x32로 맞춰 loader를 만든다.
- Generator_X는 MNIST를 SVHN-like image로, Generator_Y는 SVHN을 MNIST-like image로 변환한다.
- Discriminator_X와 Discriminator_Y는 각 domain에서 real/fake patch probability를 출력한다.
- GAN loss로 target domain realism을 학습하고, cycle consistency L1 loss로 원본 복원 가능성을 강제한다.
- 전체 iteration 기준으로 learning rate를 중간 이후 0까지 선형 감소시키는 scheduler를 구현한다.

## 구현과 이론의 연결
- CycleGAN의 핵심은 $G:X\to Y$, $F:Y\to X$에 대해 $F(G(x))\approx x$, $G(F(y))\approx y$를 강제하는 것이다.
- paired data가 없는 상황에서 adversarial loss만 쓰면 입력 내용과 무관한 target-domain 이미지로 collapse할 수 있다. cycle loss가 구조 보존 장치 역할을 한다.
- linear decay scheduler는 초반에는 0.0002 learning rate를 유지하고, decay point 이후 전체 iteration에 비례해 감소시킨다.

## 결과와 해석
- 20 epoch, 총 18,740 iteration 규모로 학습 로그가 구성되었다. 초반 Loss_D는 약 1.09, Loss_G는 약 1.84, cycle loss는 약 0.24 수준으로 시작했다.
- iteration이 진행되며 GAN loss와 cycle loss가 서로 trade-off를 보였다. 생성 품질은 단순 loss 숫자보다 변환 이미지의 domain realism과 digit identity 보존을 함께 확인해야 한다.
- Pix2Pix와의 비교에서 CycleGAN은 paired data가 없어도 학습 가능하지만, 정답쌍을 직접 사용하는 Pix2Pix보다 mapping 제약이 약하다는 결론을 냈다.

## 자주 헷갈리는 지점
- 매 iteration마다 Y loader를 새로 `next(iter(loader))`로 뽑으면 sampling이 비효율적일 수 있다. iterator 관리를 분리하는 편이 좋다.
- MNIST와 SVHN channel 수가 달라 generator/discriminator 입출력 채널을 명확히 구분해야 한다.
- cycle loss weight가 너무 크면 realistic style보다 reconstruction이 우세해지고, 너무 작으면 내용 보존이 약해진다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab12 사전보고서 Pix2Pix와 CycleGAN](23-prep-lab12-pix2pix-cyclegan.md)
- [Lab11 DCGAN](10-lab11-dcgan.md)
- [Lab10-2 Neural Style Transfer 반복 실험](09-lab10-2-neural-style-transfer.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **11. Lab12 CycleGAN**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
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
- **11. Lab12 CycleGAN**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [10. Lab11 DCGAN](10-lab11-dcgan.md) · 다음: [12. Lab13 Attention 기반 기계번역](12-lab13-attention.md)
