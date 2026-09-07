---
layout: page
title: "02. Image Processing 1 개정판"
permalink: /studies/ai/computer-vision/02-1-image-processing-1-revised/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/02-1%20Image%20Processing%201%20%EA%B0%9C%EC%A0%95%ED%8C%90.md)

{% raw %}
## 한눈에 보기
Image Processing 1 개정판은 human eye와 camera imaging pipeline에서 출발해 projection, color, image representation, 기본 filtering 개념으로 이어진다.

## 핵심 개념
- human eye
- digital camera
- projection equation
- color receptors
- linear filtering
- convolution
- edge

## 체계적 정리
- 눈의 lens와 retina 구조는 camera lens와 sensor에 대응시켜 이해할 수 있다.
- Camera obscura는 projection geometry의 직관적 모델이며, pinhole camera model의 역사적 출발점이다.
- 영상은 discrete pixel grid에 저장되므로 continuous scene에서 sampling된 신호로 볼 수 있다.
- color perception은 cone response와 RGB channel representation으로 연결된다.
- low-level vision에서는 noise suppression, smoothing, sharpening, edge extraction 같은 local operation이 중요하다.

## 중요한 수식과 관점
- Convolution은 local neighborhood와 kernel의 weighted sum이다.
- Filtering은 spatial domain convolution 또는 frequency response 관점으로 볼 수 있다.
- Projection matrix는 intrinsic과 extrinsic parameter의 곱으로 표현된다.

## 구현과 학습 포인트
개정판은 imaging pipeline 설명이 더 정리되어 있어, 뒤쪽 resampling과 pyramid를 배우기 전에 image가 어떻게 만들어지는지 잡는 데 좋다. Gaussian Pyramid 실습의 blur/downsample도 여기의 sampling 관점 위에 놓인다.

## 자주 헷갈리는 지점
- image coordinate와 camera coordinate의 원점과 축 방향을 혼동하지 않아야 한다.
- 필터링 결과는 kernel normalization, boundary handling, data type clipping에 따라 달라진다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Image Processing 통합 개요](02-image-processing-overview.md)
- [보충 Resampling과 Gaussian Pyramid](02-2-supplement-resampling-and-gaussian-pyramid.md)
- [Gaussian Pyramid 구현 노트](25-gaussian-pyramid-implementation-notes.md)

{% endraw %}

---

이전: [02. Image Processing 통합 개요](02-image-processing-overview.md) · 다음: [02. Image Processing 2](02-2-image-processing-2.md)
