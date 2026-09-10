---
layout: page
title: "04. Lab06 — Semantic Segmentation"
permalink: /studies/ai/deep-learning-experiment/04-lab06-semantic-segmentation/
sitemap: false
---

- **원본**: [GitHub — Deep Learning Experiment](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Deep_Learning_Experiment) · 사전보고서(17) + 본보고서(04) 통합·보강
- 이론 배경은 FCN·DilatedNet 논문 수준으로 보강. **실험 설정·결과 수치는 원 보고서 그대로**입니다.

{% raw %}
## 개요

이미지 전체 1개 라벨(classification)을 넘어 **모든 픽셀에 클래스 라벨**을 부여한다(semantic segmentation). VGG 기반 sliding-window의 비효율을 확인한 뒤 **FCN8s**와 **dilated convolution** 기반 개선 모델을 구현. 데이터는 PASCAL VOC 2012(background 포함 **21 클래스**).

## 1. 이론 배경

- **sliding window**: 픽셀마다 주변 patch를 CNN에 통과 → 엄청난 중복 연산, 비효율.
- **FCN(Fully Convolutional Network)**: FC층을 conv(1×1 또는 7×7)로 바꿔 **한 번의 forward로 dense score map** 출력, 임의 입력 크기 허용.
- **skip architecture**: FCN-32s → 16s → 8s. 얕은 층의 고해상도 feature를 skip으로 결합할수록 경계 디테일 복원↑.
  - FCN8s: pool3, pool4, final score를 2×·2×·8× upsampling + crop으로 정렬해 결합.
- **dilated(atrous) convolution**: pooling 없이 receptive field 확대 → dense prediction에서 context 유지. 커널 원소 사이에 간격.
  $$
  \text{padding} = \frac{\text{dilation}\times(K-1)}{2}\ \Rightarrow\ \text{feature map 크기 유지}
  $$
- **transposed convolution** = learnable upsampling.
- 평가: **mIoU** = 클래스별 $$\dfrac{TP}{TP+FP+FN}$$의 평균. class imbalance(배경 픽셀 압도)에 pixel accuracy보다 강건.

## 2. 실습 설계

- VGG16 classifier를 patch마다 적용하는 sliding-window segmentation을 먼저 관찰(비효율 확인).
- FC → conv 변환으로 fully convolutional 구조 구현.
- **FCN8s**: pool3 + pool4 + final score를 skip으로 결합.
- **MySegModel**: fc6/fc7를 **dilated conv**로 바꾸고 **context module** 추가 → receptive field 확장.

## 3. 구현 핵심

- FC → conv 변환: classification 망을 "위치별 classifier"로 재해석.
- FCN8s upsampling은 2×, 2×, 8× + **crop**으로 skip feature alignment.
- dilated conv padding = `dilation*(K-1)/2`로 크기 유지.
- pretrained VGG weight 재사용 시 fc6/fc7 weight shape과 dilation 변경의 **호환성**을 신중히.

## 4. 결과

| 모델 | mIoU |
|---|---|
| FCN8s (baseline) | **≈ 0.35** |
| MySegModel (dilated context) | **≈ 0.39** |

- 자료 요구 기준은 mIoU ≥ 0.5였으나, 제한된 iteration과 pretrained weight 호환성 문제로 그보다 낮았다. 다만 **multi-scale context가 개선 방향**임은 확인.

## 5. 해석 · 자주 틀리는 지점

- skip connection **crop boundary**가 안 맞으면 tensor addition에서 shape mismatch.
- pretrained weight 재사용 시 fc6/fc7 ↔ dilation 호환성.
- dilated conv는 **gridding artifact** 위험 → dilation rate 조합이 중요.
- mIoU는 rare class 성능 저하를 잘 드러냄 → 배경이 많은 데이터에서 pixel accuracy만 보면 과대평가.
- deconvolution으로 해상도를 복원해도 pooling에서 사라진 경계 디테일을 완전히 되살리긴 어렵다.

## 복습 질문

- sliding-window segmentation이 비효율적인 이유와 FCN이 이를 어떻게 해결하는가?
- FCN-32s / 16s / 8s의 차이(skip 사용도)와 그 효과는?
- dilated convolution이 receptive field를 키우는 방식과 padding 공식은?
- 왜 segmentation에서 pixel accuracy 대신 mIoU를 보는가?
{% endraw %}

---

이전: [03. Lab05 — FSRCNN](03-lab05-fsrcnn-super-resolution.md) · 다음: [05. Lab07 — RetinaNet & Focal Loss](05-lab07-retinanet-focal-loss.md)
