---
layout: page
title: "02. Lab04 — Spatial Transformer Network"
permalink: /studies/ai/deep-learning-experiment/02-lab04-spatial-transformer-network/
sitemap: false
---

- **원본**: [GitHub — Deep Learning Experiment](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Deep_Learning_Experiment) · 사전보고서(15) + 본보고서(02) 통합·보강
- 이론 배경은 STN 논문 수준으로 보강. **실험 설정·결과 수치는 원 보고서 그대로**입니다.

{% raw %}
## 개요

CNN은 translation에는 어느 정도 강하지만 회전·스케일·shear 같은 **기하 변형**에는 약하다. **STN**을 CNN 앞단(또는 중간)에 붙여, 별도 annotation 없이 **task loss만으로** 입력을 학습적으로 정렬한다. 왜곡된 MNIST 분류로 효과를 확인.

## 1. 이론 배경

STN은 세 부분으로 구성된다.

1. **Localization network** — 입력을 보고 변환 파라미터 $$\theta$$(affine이면 6개) 예측.
2. **Grid generator** — $$\theta$$로 출력 격자 각 점에 대응하는 **source 좌표**를 계산.
   $$
   \begin{bmatrix}x_s\\y_s\end{bmatrix}
   = A_\theta \begin{bmatrix}x_t\\y_t\\1\end{bmatrix}
   $$
3. **Sampler** — source 좌표 주변 픽셀을 **bilinear interpolation**(네 점 거리 가중합)으로 읽어 출력 생성. bilinear이라 **미분 가능** → $$\theta$$와 입력 feature 양쪽으로 gradient 전달 → end-to-end 학습.

- annotation 없이 classification loss만으로 "task에 도움이 되는 변환"만 학습.
- distorted/cluttered MNIST, fine-grained recognition처럼 **geometric nuisance가 큰** 문제에서 효과가 크다.
- affine은 translation·rotation·scale·shear까지 표현 — non-rigid deformation에는 thin-plate spline 계열이 필요.

## 2. 실습 설계

- MNIST를 **80×80**으로 키우고 회전·이동·크기 변화를 준 **distorted setting** 구성.
- `STN_CNN` = CNN backbone 앞에 localization network → affine $$\theta$$ 예측.
- `F.affine_grid` → sampling grid, `F.grid_sample`(bilinear) → transformed input.
- localization network 마지막 FC를 **identity로 초기화**: weight = 0, bias = `[1,0,0, 0,1,0]`.
- STN이 있는 모델 vs 없는 일반 CNN을 각각 학습해 accuracy·시각화 비교. lr은 `ReduceLROnPlateau`.

## 3. 구현 핵심

- **sampler가 미분 가능**해야 전체가 end-to-end로 학습된다(bilinear의 이유).
- STN 뒤 average pooling으로 크기 축소 후 backbone에 전달.
- `grid_sample`의 좌표계는 **[-1, 1] 정규화 좌표** — 픽셀 좌표와 혼동 금지.

## 4. 결과

| 모델 | best accuracy |
|---|---|
| STN + CNN | **≈ 99.34 %** |
| plain CNN | **≈ 98.42 %** |

- 이득 **≈ 0.9 %p**. MNIST 자체가 쉬워 회전 파라미터가 강하게 학습되지 않는 경우도 있었다.
- 시각화상 STN은 숫자를 **중앙으로 정렬**하려는 경향.

## 5. 해석 · 자주 틀리는 지점

- **identity 초기화**를 안 하면 학습 초기에 입력이 심하게 왜곡돼 classifier가 불안정.
- `grid_sample` 좌표계(-1~1) vs 픽셀 좌표 혼동.
- STN은 계산 overhead가 있음 → **데이터가 단순하면** 성능 이득 대비 비용을 함께 평가해야.
- STN은 feature map 중간에도 삽입 가능(그때는 learned feature의 spatial layout 정렬).

## 복습 질문

- STN의 세 구성요소와 각 역할, 그리고 sampler가 bilinear여야 하는 이유는?
- localization network의 마지막 층을 identity로 초기화하는 이유는?
- `grid_sample`의 좌표계와 흔한 좌표 실수는?
- STN의 이득이 MNIST에서 작게 나온 이유를 데이터 난이도 관점에서 설명하라.
{% endraw %}

---

이전: [01. Lab03 — VGGNet & ResNet](01-lab03-vggnet-resnet.md) · 다음: [03. Lab05 — FSRCNN](03-lab05-fsrcnn-super-resolution.md)
