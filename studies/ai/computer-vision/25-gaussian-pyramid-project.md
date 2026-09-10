---
layout: page
title: "25. Gaussian Pyramid (project)"
permalink: /studies/ai/computer-vision/25-gaussian-pyramid-project/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 과제 노트 `25(구현)` + `26(보고서)` 통합·보강
- 실습 코드는 저장소의 `Gaussian Pyramid` 폴더 참고. 이론은 [02. Image Processing](02-image-processing.md) 6~7절.

{% raw %}
## 개요

Gaussian / Laplacian pyramid를 직접 구현하고, (1) anti-aliasing 필터 유무, (2) filtering gain, (3) 노이즈 추가가 **복원 품질(PSNR)**에 미치는 영향을 실험한다.

## 파이프라인

```
image ─► build_gaussian_pyramid ─► build_laplacian_pyramid
                                        │
                                        ▼
                       reconstruct_from_laplacian ─► PSNR 비교
```

## 핵심 함수와 역할

| 함수 | 하는 일 |
|---|---|
| `gaussian_kernel_1d(σ)` | 1D Gaussian 커널 생성 후 합 1로 정규화 |
| `sep_conv(img, k1d)` | 수평 conv → 수직 conv (separable) = 2D Gaussian filtering, $$O(N^2K)$$ |
| `downsample2(img)` | **blur 먼저**, 그다음 2배 decimation (anti-aliasing) |
| `upsample2(img)` | 0 삽입(또는 resize) 후 Gaussian filtering으로 보간 |
| `build_gaussian_pyramid` | $$G_0=I$$, $$G_{i+1}=\text{downsample2}(G_i)$$ — 해상도 1/2씩 |
| `build_laplacian_pyr` | $$L_i = G_i - \text{upsample2}(G_{i+1})$$, 최상위는 $$G_{\text{top}}$$ 저장 |
| `reconstruct_from_lap` | 최상위 Gaussian + Laplacian residual을 아래로 누적하며 더해 원본 복원 |
| `psnr(a, b)` | MSE 기반 품질 수치 |

## 주요 식

$$
L_i = G_i - \text{upsample}(G_{i+1}),\qquad
G_i = L_i + \text{upsample}(G_{i+1})
$$
$$
\text{PSNR} = 10\,\log_{10}\!\frac{\text{MAX}^2}{\text{MSE}}
$$

- **separable Gaussian**: 2D 커널을 1D 두 번으로 분해 → 연산량 대폭 절감.
- Laplacian pyramid는 Gaussian pyramid에서 **사라지는 고주파 성분을 residual로 보존** → 무손실 복원 가능(수치오차 제외).

## 실험에서 확인할 점

- **blur 없이 downsample** → edge 주변 aliasing 증가, 복원 PSNR 하락.
- **Gaussian이 너무 강함** → aliasing↓ 이지만 detail 손실 → 역시 PSNR 최적점 존재.
- **노이즈 추가**(salt-and-pepper / Gaussian) → Laplacian residual에 노이즈가 실려 복원 품질 저하. Gaussian 노이즈는 blur에 일부 억제되지만 impulse 노이즈는 median 계열이 유리.
- upsample만으로는 잃은 detail 복원 불가 — residual이 있어야 한다.

## 구현 시 함정

- 모든 이미지를 **float, 0~1** 범위로 유지. `uint8` 혼용 시 오버플로/클리핑.
- downsample/upsample에서 **크기·경계(boundary) 정합**을 맞춰야 residual 덧셈이 성립(홀수 크기 주의).
- PSNR 값은 data range가 1인지 255인지에 따라 달라진다 — 비교 조건 통일.
- 커널 반경을 $$\approx 3\sigma$$ 이상으로 잡아야 Gaussian이 잘리지 않는다.

## 복습 질문

- separable convolution이 왜 성립하며 연산량 이득은 얼마인가?
- Laplacian pyramid의 정의와, 원본을 복원하는 재귀식은?
- anti-aliasing 필터를 빼면 무엇이, 왜 나빠지는가? 필터가 너무 강하면?
- PSNR 계산에서 data range를 명시해야 하는 이유는?

{% endraw %}

---

이전: [13. Two-view Geometry](13-two-view-geometry.md) · 다음: [27. ResNet (project)](27-resnet-project.md)
