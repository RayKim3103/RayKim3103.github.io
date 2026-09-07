---
layout: page
title: "03. Lab05 FSRCNN 초해상도"
permalink: /studies/ai/deep-learning-experiment/03-lab05-fsrcnn/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/03%20Lab05%20FSRCNN%20%EC%B4%88%ED%95%B4%EC%83%81%EB%8F%84.md)

## 한눈에 보기
저해상도 이미지를 고해상도로 복원하는 super-resolution 실습이다. SRCNN의 bicubic 선확대 방식 대신 FSRCNN처럼 저해상도 공간에서 feature extraction과 mapping을 수행하고 마지막 transposed convolution으로 해상도를 키운다.

## 핵심 개념
- single image super-resolution
- SRCNN
- FSRCNN
- deconvolution
- T91
- Set5
- PSNR
- YCbCr Y-channel

## 실습 흐름
- T91 training image에서 64x64 HR patch를 만들고 scale factor 4에 맞춰 LR/HR 쌍을 구성한다.
- horizontal flip, 0/90/180/270도 rotation 등 간단한 augmentation을 적용한다.
- Set5 test set은 YCbCr로 변환해 Y-channel을 모델 입력과 PSNR 평가에 사용한다.
- FSRCNN은 feature extraction, shrinking, nonlinear mapping, expanding, deconvolution 순서로 구현한다.
- MSE loss로 학습하고, test PSNR이 가장 높은 모델을 저장한 뒤 bicubic 결과와 시각적으로 비교한다.

## 구현과 이론의 연결
- FSRCNN의 핵심은 비싼 연산을 HR 공간이 아니라 LR 공간에서 수행하는 것이다. shrinking 1x1 convolution은 채널 수를 줄여 mapping layer의 계산량을 낮추고, expanding은 복원 직전에 표현력을 회복한다.
- 마지막 ConvTranspose2d는 scale factor 4에 맞춰 16x16 LR feature를 64x64 HR patch로 키운다. padding과 output padding을 맞추지 않으면 출력 크기가 ground truth와 달라진다.
- PSNR은 $10\log_{10}(MAX_I^2/MSE)$ 관점의 지표로, 값이 클수록 pixel-level 복원이 좋다. border artifact를 줄이기 위해 scale factor만큼 테두리를 shave한 뒤 평가한다.

## 결과와 해석
- Bicubic interpolation의 PSNR은 약 27.66 dB 수준으로 일정했다.
- FSRCNN은 학습 epoch가 증가하며 PSNR이 점진적으로 향상되었고, 약 80 epoch 이후 bicubic보다 안정적으로 높은 PSNR을 보였다.
- 시각화에서는 Y-channel은 FSRCNN 출력, Cb/Cr은 bicubic에서 가져와 RGB로 복원했다. 세부 edge와 질감이 bicubic보다 더 선명해지는 효과를 확인했다.

## 자주 헷갈리는 지점
- 학습과 평가는 Y-channel 기준이므로 RGB 전체를 직접 MSE로 비교한 결과와 다를 수 있다.
- test set으로 best model을 고르는 것은 실습 편의상 허용된 설정이지만, 실제 연구에서는 validation set을 분리해야 한다.
- ConvTranspose2d 출력 크기 공식과 scale factor를 항상 함께 검산해야 한다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab05 사전보고서 FSRCNN](16-prep-lab05-fsrcnn.md)
- [Lab10 Neural Style Transfer](08-lab10-neural-style-transfer.md)
- [Lab10-2 Neural Style Transfer 반복 실험](09-lab10-2-neural-style-transfer.md)


---

이전: [02. Lab04 Spatial Transformer Network](02-lab04-spatial-transformer-network.md) · 다음: [04. Lab06 Semantic Segmentation](04-lab06-semantic-segmentation.md)
