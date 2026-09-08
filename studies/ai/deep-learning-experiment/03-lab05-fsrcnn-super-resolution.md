---
layout: page
title: "03. Lab05 — FSRCNN (Super-Resolution)"
permalink: /studies/ai/deep-learning-experiment/03-lab05-fsrcnn-super-resolution/
sitemap: false
---

- **원본**: [GitHub — Deep Learning Experiment](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Deep_Learning_Experiment) · 사전보고서(16) + 본보고서(03) 통합·보강
- 이론 배경은 SRCNN/FSRCNN 논문 수준으로 보강. **실험 설정·결과 수치는 원 보고서 그대로**입니다.

{% raw %}
## 개요

저해상도(LR) 이미지를 고해상도(HR)로 복원하는 **single image super-resolution**. SRCNN처럼 bicubic으로 먼저 키운 뒤 처리하는 방식 대신, **FSRCNN**처럼 LR 공간에서 feature extraction·mapping을 하고 마지막 **transposed convolution**에서만 해상도를 키운다(scale factor 4).

## 1. 이론 배경 — SRCNN vs FSRCNN

- **SRCNN**: LR을 bicubic으로 HR 크기까지 확대 → CNN 3층. 모든 연산이 HR 공간이라 **비쌈**.
- **FSRCNN**: LR을 그대로 입력, 파이프라인 = `feature extraction → shrinking → nonlinear mapping → expanding → deconvolution`.
  - **shrinking**(1×1 conv): 채널 축소 → mapping layer 연산량↓.
  - **expanding**(1×1 conv): deconvolution 직전 표현력 회복.
  - **deconvolution**(ConvTranspose2d): learnable upsampling으로 HR 복원.
- SR은 없는 고주파를 **추정**하는 문제 → 단순 interpolation보다 data prior를 학습한 CNN이 유리.

### 주요 식
$$
\text{MSE} = \frac{1}{N}\sum_i (\hat y_i - y_i)^2,\qquad
\text{PSNR} = 10\log_{10}\!\frac{\text{MAX}_I^2}{\text{MSE}}
$$
PSNR이 클수록 픽셀 단위 복원이 좋음. SR에서는 보통 **Y-channel**로 평가.

## 2. 실습 설계

- **T91** training image에서 64×64 HR patch → scale 4에 맞춰 LR(16×16)/HR 쌍 구성.
- augmentation: horizontal flip, 0/90/180/270° rotation.
- **Set5** test set은 **YCbCr**로 변환, **Y-channel**만 입력·PSNR 평가에 사용.
- FSRCNN을 위 5단계로 구현, **MSE loss**로 학습. test PSNR 최고 모델 저장 후 bicubic과 시각 비교.
- layer별 learning rate, Adam optimizer.

## 3. 구현 핵심

- 핵심은 비싼 연산을 HR이 아닌 **LR 공간**에서 수행 → 구조적 속도 이점.
- 마지막 `ConvTranspose2d`는 16×16 → 64×64. **padding·output_padding**을 안 맞추면 출력 크기가 ground truth와 달라진다 → 출력 크기 공식과 scale factor를 항상 함께 검산.
- border artifact를 줄이려 평가 시 **scale factor만큼 테두리를 shave** 후 PSNR 계산.

## 4. 결과

| | PSNR |
|---|---|
| Bicubic | **≈ 27.66 dB** (일정) |
| FSRCNN | epoch 증가에 따라 점진 향상, **약 80 epoch 이후 bicubic보다 안정적으로 높음** |

- 시각화: Y-channel은 FSRCNN 출력, Cb/Cr은 bicubic에서 가져와 RGB 복원 → edge·질감이 bicubic보다 선명.

## 5. 해석 · 자주 틀리는 지점

- 학습·평가가 **Y-channel** 기준 → RGB 전체 MSE 결과와 다를 수 있다.
- test set으로 best model을 고른 건 실습 편의(실연구는 validation 분리 필요).
- deconvolution은 **checkerboard artifact** 위험 → kernel/stride/padding 신중히.
- PSNR이 높다고 항상 가장 자연스러운 이미지는 아님 → SSIM·perceptual metric 병행 고려.

## 복습 질문

- SRCNN 대비 FSRCNN이 구조적으로 빠른 이유는?
- shrinking / expanding 1×1 conv 각각의 역할은?
- ConvTranspose2d 출력 크기와 scale factor를 어떻게 검산하는가?
- SR을 Y-channel로 평가하는 이유와, PSNR의 한계는?
{% endraw %}

---

이전: [02. Lab04 — Spatial Transformer Network](02-lab04-spatial-transformer-network.md) · 다음: [04. Lab06 — Semantic Segmentation](04-lab06-semantic-segmentation.md)
