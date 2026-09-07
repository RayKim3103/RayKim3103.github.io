---
layout: page
title: "02. Image Processing"
permalink: /studies/ai/computer-vision/02-image-processing/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `02` + `02-1(개정판)` + `02-2` + `02-2 보충` 통합·보강
- 표준 CV 교재(Szeliski · CS231n) 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

저수준 영상 처리는 (1) 이미지가 어떻게 **형성**되는가, (2) 이미지를 어떻게 **표현**하는가, (3) 선형 filtering으로 어떻게 **특징(edge 등)**을 뽑는가, (4) 해상도를 바꿀 때 생기는 **sampling/aliasing** 문제와 **pyramid**를 다룬다. 이후 CNN의 convolution·pooling·multi-scale 처리가 전부 여기서 나온다.

## 1. 영상 형성 (image formation)

### Pinhole camera
3D 점 $X=(X,Y,Z)$이 초점거리 $f$인 바늘구멍을 지나 상 평면에 맺힌다.

$$
x = f\frac{X}{Z}, \qquad y = f\frac{Y}{Z}
$$

원근 투영(perspective projection)은 **비선형**(1/Z)이며, 멀리 있는 물체가 작게 보이는 이유다. 동차좌표(homogeneous coordinate)로 쓰면 선형이 된다:

$$
s\begin{bmatrix}u\\v\\1\end{bmatrix}
= K\,[\,R \mid t\,]\begin{bmatrix}X\\Y\\Z\\1\end{bmatrix},
\qquad
K=\begin{bmatrix}f_x & s & c_x\\ 0 & f_y & c_y\\ 0 & 0 & 1\end{bmatrix}
$$

$K$ = intrinsic matrix(초점거리 픽셀 단위 $f_x,f_y$, 주점 $c_x,c_y$), $[R\mid t]$ = extrinsic(카메라 자세). → 자세한 내용은 [12. Camera Calibration](12-camera-calibration.md).

### 색 (color)
사람 눈의 원추세포는 L/M/S 3종이므로 색은 3차원으로 근사된다. 디지털 카메라는 RGB Bayer filter로 이를 흉내낸다. 색공간(RGB, HSV, Lab)은 목적에 따라 선택한다.

## 2. 이미지 표현

- grayscale 이미지 = 2D 배열 $I[y,x]\in[0,255]$ 또는 정규화하면 $[0,1]$.
- 컬러 = 3D tensor $I[y,x,c]$, $c\in\{R,G,B\}$.
- 학습에서는 float, 0~1(또는 평균 0 정규화)로 유지하는 것이 안전하다.

## 3. 선형 filtering과 convolution

출력 픽셀 = 커널 $k$로 가중한 이웃 픽셀의 합.

$$
(I * k)[y,x] = \sum_{i}\sum_{j} I[y-i,\,x-j]\,k[i,j]
$$

- **평균/박스 필터, Gaussian 필터** → blur(저역통과). Gaussian은 $k(i,j)\propto e^{-(i^2+j^2)/2\sigma^2}$.
- **separable**: 2D Gaussian = (1D 수평) ∘ (1D 수직). $O(N^2 K^2)\to O(N^2 K)$ 로 연산량 감소.
- **미분 필터**(Sobel 등) → gradient(고역통과).
- correlation vs convolution: convolution은 커널을 뒤집는다. 대칭 커널(Gaussian)에서는 결과가 같다.
- 경계 처리: zero-pad / reflect / replicate — 선택에 따라 가장자리 artifact가 달라진다.

## 4. Edge detection — Canny

1. **Gaussian smoothing** — 노이즈 억제(미분은 노이즈를 증폭하므로 필수).
2. **gradient 계산** — 크기 $\lvert\nabla I\rvert=\sqrt{I_x^2+I_y^2}$, 방향 $\theta=\operatorname{atan2}(I_y,I_x)$.
3. **non-maximum suppression (NMS)** — gradient 방향으로 국소 최대만 남겨 edge를 1픽셀 폭으로.
4. **hysteresis thresholding** — 높은 임계값 이상은 확정 edge, 낮은 임계값 이상이면서 확정 edge에 연결된 것만 유지 → 끊김 없이 잡음 억제.

## 5. Morphology (이진 영상)

구조요소(structuring element)로 형태를 다듬는다.

- **erosion**(침식): 객체 축소, 작은 잡점 제거.
- **dilation**(팽창): 객체 확대, 구멍 메움.
- **opening** = erosion → dilation: 잡점 제거하며 크기 보존.
- **closing** = dilation → erosion: 구멍·틈 메움.

## 6. Sampling과 Aliasing

연속 신호를 격자에 sampling할 때, sampling 주파수가 낮으면 **서로 다른 연속 신호가 같은 샘플로 보인다**(aliasing). 이산 sinusoid는 $[-\pi,\pi]$ 범위의 주파수만 표현할 수 있어, 그 밖의 고주파는 낮은 주파수로 "접혀" 들어온다.

$$
\textbf{Nyquist–Shannon: } \quad F_s > 2 F_{\max}
$$

이미지에서 aliasing은 축소 시 물결무늬(moiré), 계단 현상으로 나타난다.

### 올바른 downsampling
$$
\text{downsample}(I) = \text{decimate}\big(\,I * g_\sigma\,\big)
$$
먼저 **Gaussian 저역통과 필터**로 고주파를 제거하고 그다음 격자 추출(decimation). blur 없이 격자만 빼면(순수 subsampling) aliasing이 커진다. 반대로 blur가 너무 강하면 aliasing은 줄지만 detail도 사라진다 → trade-off.

## 7. Gaussian / Laplacian pyramid

- **Gaussian pyramid**: $G_0 = I$, $G_{i+1} = \text{downsample}(G_i * g)$ — 해상도를 1/2씩 낮춘 **scale-space**. 크기가 다른 물체/특징을 같은 커널로 처리 가능.
- **Laplacian pyramid**: $L_i = G_i - \text{upsample}(G_{i+1})$ — 각 레벨의 **고주파 residual**. 다시 위로 더하면 원본 복원 가능.
- **upsampling / interpolation**: 빈 격자값을 이웃으로 추정(nearest / bilinear / bicubic). upsample만으로는 잃은 detail을 되살릴 수 없다(그래서 Laplacian residual이나 학습 기반 super-resolution이 필요).

→ 구현은 [25. Gaussian Pyramid (project)](25-gaussian-pyramid-project.md).

## 자주 틀리는 지점

- "subsampling"과 "downsampling"을 혼용하지만, 올바른 downsampling에는 **anti-aliasing 필터**가 포함되어야 한다.
- convolution은 커널을 뒤집는다 — 비대칭 커널(미분)에서는 correlation과 부호가 달라진다.
- 미분 전에 smoothing을 안 하면 노이즈가 gradient를 지배한다.
- Gaussian blur는 $\sigma$가 클수록 저주파만 남긴다 — $\sigma$와 커널 반경을 함께 키워야 잘림 없이 근사된다(보통 반경 $\approx 3\sigma$).

## 복습 질문

- 원근 투영이 왜 비선형이며, 동차좌표로 어떻게 선형화되는가?
- Canny의 4단계를 순서대로, 각 단계의 목적과 함께 설명하라.
- aliasing이 왜 생기며, downsampling pipeline에서 Gaussian 필터의 위치와 이유는?
- Gaussian pyramid와 Laplacian pyramid의 정의와, 둘의 관계는?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **02. Image Processing**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 기하/영상처리 주제에서는 좌표계, scale, interpolation, boundary condition을 문제 시작에 명시한다.
- 동차좌표의 scale ambiguity와 실제 pixel 단위 오차를 구분한다.
- 이미지 처리 연산은 필터 모양뿐 아니라 경계 조건, sampling, aliasing, scale 변화까지 함께 봐야 한다.
- 분류, 검출, 분할 모델은 architecture보다 입력 표현, loss, annotation 형식, metric의 차이가 중요하다.
- 3D vision에서는 좌표계, 카메라 모델, homogeneous coordinate, rank/scale ambiguity를 계속 추적해야 한다.

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
- **02. Image Processing**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [01. Introduction](01-introduction.md) · 다음: [03. Classification](03-classification.md)
