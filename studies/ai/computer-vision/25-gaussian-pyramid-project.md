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
| `sep_conv(img, k1d)` | 수평 conv → 수직 conv (separable) = 2D Gaussian filtering, $O(N^2K)$ |
| `downsample2(img)` | **blur 먼저**, 그다음 2배 decimation (anti-aliasing) |
| `upsample2(img)` | 0 삽입(또는 resize) 후 Gaussian filtering으로 보간 |
| `build_gaussian_pyramid` | $G_0=I$, $G_{i+1}=\text{downsample2}(G_i)$ — 해상도 1/2씩 |
| `build_laplacian_pyr` | $L_i = G_i - \text{upsample2}(G_{i+1})$, 최상위는 $G_{\text{top}}$ 저장 |
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
- 커널 반경을 $\approx 3\sigma$ 이상으로 잡아야 Gaussian이 잘리지 않는다.

## 복습 질문

- separable convolution이 왜 성립하며 연산량 이득은 얼마인가?
- Laplacian pyramid의 정의와, 원본을 복원하는 재귀식은?
- anti-aliasing 필터를 빼면 무엇이, 왜 나빠지는가? 필터가 너무 강하면?
- PSNR 계산에서 data range를 명시해야 하는 이유는?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **25. Gaussian Pyramid (project)**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 기하/영상처리 주제에서는 좌표계, scale, interpolation, boundary condition을 문제 시작에 명시한다.
- 동차좌표의 scale ambiguity와 실제 pixel 단위 오차를 구분한다.
- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- 이미지 처리 연산은 필터 모양뿐 아니라 경계 조건, sampling, aliasing, scale 변화까지 함께 봐야 한다.

### 문제 풀이 또는 구현 루틴

- 문제를 2D appearance, 3D geometry, learning 문제 중 어디에 가까운지 먼저 분류한다.
- 수식이 나오면 coordinate frame과 tensor shape을 함께 적어 projection과 feature map 흐름을 놓치지 않는다.
- 실험 보고서는 qualitative image와 quantitative metric을 나란히 두고 failure case를 설명한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 시각적으로 좋아 보이는 결과가 metric상 좋은 결과와 항상 일치하지 않는다.
- resize, padding, normalization 같은 preprocessing 차이가 모델 성능을 크게 바꿀 수 있다.
- calibration과 two-view geometry에서는 scale이 정해지지 않는 값이 많아 절대 크기로 해석하면 안 된다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 방법이 조명, viewpoint, scale 변화에 어떻게 반응하는가?
- loss와 metric이 실제 원하는 시각 품질 또는 인식 성능을 잘 대변하는가?
- 실패 사례를 데이터 부족, 모델 capacity, 최적화, annotation noise 중 무엇으로 설명할 수 있는가?
- **25. Gaussian Pyramid (project)**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [13. Two-view Geometry](13-two-view-geometry.md) · 다음: [27. ResNet (project)](27-resnet-project.md)
