---
layout: page
title: "26. Gaussian Pyramid 보고서"
permalink: /studies/ai/computer-vision/26-gaussian-pyramid-report/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/26%20Gaussian%20Pyramid%20%EB%B3%B4%EA%B3%A0%EC%84%9C.md)

{% raw %}
## 한눈에 보기
Gaussian Pyramid 보고서는 실습 pipeline과 각 함수의 역할, 이미지 복원 결과를 설명한 과제 보고서이다. decimation/interpolation과 Gaussian/Laplacian pyramid의 관계를 단계적으로 정리한다.

## 핵심 개념
- decimation
- interpolation
- Gaussian pyramid
- Laplacian pyramid
- separable filter
- image reconstruction
- salt pepper noise
- Gaussian noise

## 체계적 정리
- decimation은 sample 수를 줄이는 과정이고, interpolation은 낮은 해상도 grid에서 빠진 값을 추정해 해상도를 키우는 과정이다.
- Gaussian pyramid는 blur와 downsample을 반복해 low-frequency 중심의 multi-scale representation을 만든다.
- Laplacian pyramid는 각 level에서 Gaussian image와 다음 level upsample 결과의 차이를 저장해 high-frequency 정보를 보존한다.
- reconstruction은 top Gaussian image에서 시작해 Laplacian residual을 순서대로 더해 원래 이미지를 복원한다.
- gain 조정은 level별 detail 성분을 강화하거나 약화해 sharpening/denoising 같은 효과를 만든다.
- Gaussian noise와 salt-and-pepper noise를 추가한 뒤 pyramid filtering이 품질에 미치는 영향을 PSNR로 비교한다.

## 중요한 수식과 관점
- Gaussian pyramid: $G_{i+1}=downsample(G_i*k)$.
- Laplacian pyramid: $L_i=G_i-upsample(G_{i+1})$.
- MSE와 PSNR은 복원 품질의 기본 정량 지표다.

## 구현과 학습 포인트
보고서의 함수 설명은 ipynb 구현과 일대일로 대응한다. `sep_conv`, `downsample2`, `upsample2`, `build_laplacian_pyr`, `reconstruct_from_lap`의 입출력 shape을 함께 확인하면 디버깅이 쉬워진다.

## 자주 헷갈리는 지점
- Laplacian pyramid를 단순 edge map으로만 보면 안 된다. reconstruction 가능한 residual representation이다.
- noise 제거와 detail 보존은 trade-off가 있으므로 gain을 높이면 선명도와 잡음이 함께 커질 수 있다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Gaussian Pyramid 구현 노트](25-gaussian-pyramid-implementation-notes.md)
- [보충 Resampling과 Gaussian Pyramid](02-2-supplement-resampling-and-gaussian-pyramid.md)
- [Image Processing 2](02-2-image-processing-2.md)

{% endraw %}

---

이전: [25. Gaussian Pyramid 구현 노트](25-gaussian-pyramid-implementation-notes.md) · 다음: [27. ResNet 보고서](27-resnet-report.md)
