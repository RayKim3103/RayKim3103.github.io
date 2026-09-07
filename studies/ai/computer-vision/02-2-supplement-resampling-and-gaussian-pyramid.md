---
layout: page
title: "02. 보충 Resampling과 Gaussian Pyramid"
permalink: /studies/ai/computer-vision/02-2-supplement-resampling-and-gaussian-pyramid/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/02-2%20%EB%B3%B4%EC%B6%A9%20Resampling%EA%B3%BC%20Gaussian%20Pyramid.md)

{% raw %}
## 한눈에 보기
Image Processing 2 보충 자료는 resampling과 Gaussian pyramid를 더 신호처리적으로 설명한다. continuous-time sinusoid와 discrete-time sinusoid의 차이를 통해 aliasing이 왜 생기는지 보여준다.

## 핵심 개념
- sampling theorem
- aliasing
- Gaussian pre-filtering
- Gaussian pyramid
- upsampling
- image interpolation
- super-resolution

## 체계적 정리
- 연속 시간 신호는 무한한 주파수 표현을 가질 수 있지만, 이산 시간 신호는 sampling frequency에 의해 표현 가능한 주파수 범위가 제한된다.
- sampling frequency가 충분히 높지 않으면 서로 다른 연속 신호가 같은 discrete samples로 보이는 aliasing이 생긴다.
- 이미지를 줄일 때 Gaussian pre-filtering을 먼저 적용하면 high-frequency 성분을 줄여 aliasing을 완화한다.
- Gaussian pyramid는 이미지 해상도를 1/2, 1/4, 1/8로 낮추며 scale-space를 구성한다.
- upsampling과 interpolation은 빈 grid 값을 주변 pixel로 추정하는 과정이다.

## 중요한 수식과 관점
- Sampling theorem: $F_s > 2F_{max}$.
- Downsampling pipeline: low-pass filtering 후 decimation.
- Pyramid level: $G_{i+1}=downsample(G_i * k)$.

## 구현과 학습 포인트
Gaussian Pyramid 과제에서는 이 보충 자료의 sampling 관점이 그대로 쓰인다. blur 없이 decimation하면 edge 주변 aliasing이 커지고, reconstruction 품질도 떨어진다.

## 자주 헷갈리는 지점
- subsampling과 downsampling은 비슷하게 쓰이지만, 올바른 downsampling에는 anti-aliasing filter가 포함되어야 한다.
- Gaussian blur가 너무 강하면 aliasing은 줄지만 detail도 사라진다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Image Processing 2](02-2-image-processing-2.md)
- [Gaussian Pyramid 구현 노트](25-gaussian-pyramid-implementation-notes.md)
- [Gaussian Pyramid 보고서](26-gaussian-pyramid-report.md)

{% endraw %}

---

이전: [02. Image Processing 2](02-2-image-processing-2.md) · 다음: [03. Classification 통합](03-classification-overview.md)
