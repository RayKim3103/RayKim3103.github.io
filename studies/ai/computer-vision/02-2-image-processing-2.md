---
layout: page
title: "02. Image Processing 2"
permalink: /studies/ai/computer-vision/02-2-image-processing-2/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/02-2%20Image%20Processing%202.md)

{% raw %}
## 한눈에 보기
Image Processing 2는 edge detection, morphology, sampling theorem, aliasing, Gaussian pyramid와 interpolation을 다루는 자료이다. low-level feature extraction과 multi-scale representation의 핵심이 담겨 있다.

## 핵심 개념
- Canny edge detector
- gradient
- non-maximum suppression
- hysteresis
- morphology
- resampling
- aliasing
- Gaussian pyramid

## 체계적 정리
- Canny edge detector는 Gaussian derivative filtering, gradient magnitude/orientation 계산, non-maximum suppression, double threshold, hysteresis tracking 순서로 동작한다.
- morphological operation은 binary 또는 mask image에서 erosion, dilation, opening, closing으로 구조를 다룬다.
- resampling은 continuous image를 다른 grid로 옮기는 과정이며 interpolation이 필요하다.
- subsampling 전에 low-pass filtering을 하지 않으면 aliasing이 발생한다.
- Gaussian pyramid는 blur와 downsampling을 반복해 multi-scale image representation을 만든다.

## 중요한 수식과 관점
- Gradient magnitude: $\sqrt{I_x^2+I_y^2}$.
- Nyquist 조건: sampling frequency는 신호 최대 주파수의 두 배보다 커야 한다.
- Opening: erosion 후 dilation, Closing: dilation 후 erosion.

## 구현과 학습 포인트
실습에서는 Gaussian kernel을 separable하게 적용하고 downsample/upsample/reconstruction을 구현한다. edge, pyramid, resampling은 CNN 이전 시대의 feature engineering이면서 현대 vision에서도 preprocessing과 multi-scale feature의 기초다.

## 자주 헷갈리는 지점
- non-maximum suppression은 gradient 방향을 따라 local maximum만 남기는 과정이다.
- dilation과 erosion은 밝기 image의 max/min filter처럼 확장될 수 있지만, 기본 직관은 binary shape 변화에서 출발한다.
- upsampling은 정보를 새로 만드는 것이 아니라 보간하는 것이므로 lost high-frequency를 완전히 복구하지 못한다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [보충 Resampling과 Gaussian Pyramid](02-2-supplement-resampling-and-gaussian-pyramid.md)
- [Gaussian Pyramid 구현 노트](25-gaussian-pyramid-implementation-notes.md)
- [Detection and Segmentation](06-detection-and-segmentation.md)

{% endraw %}

---

이전: [02. Image Processing 1 개정판](02-1-image-processing-1-revised.md) · 다음: [02. 보충 Resampling과 Gaussian Pyramid](02-2-supplement-resampling-and-gaussian-pyramid.md)
