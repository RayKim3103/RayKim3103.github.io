---
layout: page
title: "25. Gaussian Pyramid 구현 노트"
permalink: /studies/ai/computer-vision/25-gaussian-pyramid-implementation-notes/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/25%20Gaussian%20Pyramid%20%EA%B5%AC%ED%98%84%20%EB%85%B8%ED%8A%B8.md)

{% raw %}
## 한눈에 보기
Gaussian Pyramid ipynb 실습은 Gaussian/Laplacian pyramid를 직접 구현하고, filtering gain 조정과 noise 추가가 reconstruction quality에 미치는 영향을 확인하는 과제이다.

## 핵심 개념
- Gaussian kernel
- separable convolution
- downsample2
- upsample2
- Laplacian pyramid
- reconstruction
- noise
- PSNR

## 체계적 정리
- `gaussian_kernel_1d()`는 1D Gaussian kernel을 만들고 normalize한다.
- `sep_conv(img, k1d)`는 horizontal convolution과 vertical convolution을 나누어 2D Gaussian filtering과 같은 효과를 효율적으로 낸다.
- `downsample2`는 blur 후 2배 decimation을 수행해 aliasing을 줄인다.
- `upsample2`는 0 삽입 또는 resize 후 Gaussian filtering으로 상위 해상도 이미지를 복원한다.
- `build_gaussian_pyramid`는 여러 level의 저해상도 이미지를 만들고, `build_laplacian_pyr`는 level 간 차이를 저장한다.
- `reconstruct_from_lap`는 top Gaussian image와 Laplacian residual을 더해 원 이미지를 복원한다.
- `psnr`은 원본과 복원 결과의 MSE 기반 품질을 수치화한다.

## 중요한 수식과 관점
- Separable Gaussian: 2D kernel을 1D horizontal과 vertical convolution으로 분해한다.
- Laplacian residual: $L_i=G_i-upsample(G_{i+1})$.
- PSNR: $10\log_{10}(MAX^2/MSE)$.

## 구현과 학습 포인트
구현 포인트는 모든 image를 float 0~1 범위로 유지하고, downsample/upsample 과정에서 크기와 boundary를 맞추는 것이다. Laplacian pyramid는 Gaussian pyramid에서 사라지는 high-frequency 성분을 residual로 보존한다.

## 자주 헷갈리는 지점
- blur 없이 downsample하면 aliasing이 커진다.
- upsample만으로는 손실된 detail을 되살릴 수 없고 Laplacian residual이 필요하다.
- PSNR 비교는 data range가 1인지 255인지에 따라 값이 달라진다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Gaussian Pyramid 보고서](26-gaussian-pyramid-report.md)
- [보충 Resampling과 Gaussian Pyramid](02-2-supplement-resampling-and-gaussian-pyramid.md)
- [Image Processing 2](02-2-image-processing-2.md)

{% endraw %}

---

이전: [13. Two-view Geometry 2](13-2-two-view-geometry-2.md) · 다음: [26. Gaussian Pyramid 보고서](26-gaussian-pyramid-report.md)
