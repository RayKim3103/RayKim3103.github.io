---
layout: page
title: "11. 3D Geometry"
permalink: /studies/ai/computer-vision/11-3d-geometry/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `11(3D Geometry 1)` 보강
- 표준 교재(Hartley–Zisserman · Szeliski) 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

3D 비전은 2D 이미지와 3D 세계의 관계를 **projective geometry**로 기술한다. 핵심 도구는 (1) pinhole camera model, (2) homogeneous coordinate, (3) 변환 계층(Euclidean → similarity → affine → projective).

## 1. Homogeneous coordinate

$n$차원 점을 $(n{+}1)$차원 벡터로: $(x,y) \to (x,y,1)$, 그리고 $\lambda(x,y,1)$은 같은 점.

- **무한원점**(point at infinity): $(x,y,0)$ — 평행선의 교점(소실점 vanishing point).
- 장점: 이동·회전·투영을 **모두 행렬 곱**으로 표현. 원근투영의 $1/Z$ 비선형성이 선형이 된다.
- 이미지 좌표로 되돌릴 때 마지막 성분으로 나눈다(perspective divide).

## 2. Pinhole camera model

$$
s\begin{bmatrix}u\\v\\1\end{bmatrix}
= \underbrace{\begin{bmatrix}f_x & s_k & c_x\\ 0 & f_y & c_y\\ 0 & 0 & 1\end{bmatrix}}_{K\ \text{(intrinsic)}}
\underbrace{\big[\,R \mid t\,\big]}_{\text{extrinsic}}
\begin{bmatrix}X\\Y\\Z\\1\end{bmatrix}
$$

- $K$: 초점거리(픽셀) $f_x,f_y$, 주점 $c_x,c_y$, skew $s_k$(보통 0).
- $[R\mid t]$: 월드→카메라 좌표 회전·이동 (6 DOF).
- $P = K[R\mid t]$ : $3\times4$ **projection matrix**, 11 DOF(스케일 자유도 제외).
- 깊이 $Z$는 투영에서 사라진다 → 단일 이미지로는 스케일 모호(작은 가까운 물체 vs 큰 먼 물체).

## 3. 변환 계층 (2D/3D 공통)

| 변환 | 자유도(2D) | 보존하는 것 | 행렬 형태 |
|---|---|---|---|
| Euclidean (rigid) | 3 | 길이, 각도, 넓이 | $\begin{bmatrix}R & t\\ 0 & 1\end{bmatrix}$, $R^\top R=I$ |
| Similarity | 4 | 각도, 비율 | 위 + 스칼라 $s$ |
| Affine | 6 | 평행성, 넓이 비 | $\begin{bmatrix}A & t\\ 0 & 1\end{bmatrix}$ |
| Projective (homography) | 8 | 공선성(collinearity), cross-ratio | 일반 $3\times3$ (마지막 행 임의) |

- **rigid transform**: 강체 이동(회전 3 + 이동 3, 3D에서 6 DOF).
- **homography** $H$($3\times3$): 평면↔평면 사상. 순수 회전 카메라, 평면 장면, 파노라마 stitching에서 두 이미지 관계가 정확히 homography.

## 4. 회전 표현

$3\times3$ 회전행렬($R^\top R = I$, $\det R = 1$), 축-각(axis-angle, Rodrigues), 쿼터니언, 오일러각. 최적화에서는 특이점 없는 쿼터니언/축-각을 선호.

## 자주 틀리는 지점

- homogeneous 벡터는 스케일 무시 → 비교·정규화 시 마지막 성분으로 나눠야 한다.
- intrinsic $K$는 카메라 고유(초점·센서), extrinsic $[R\mid t]$는 자세 — 캘리브레이션에서 분리 추정.
- 단일 이미지에서 절대 스케일은 알 수 없다(추가 정보: 물체 크기, 스테레오 baseline, IMU 등 필요).
- projection matrix $P$는 11 DOF(전체 스케일 1 자유도 제외).

## 복습 질문

- homogeneous coordinate의 이점 두 가지와 무한원점의 의미는?
- $P = K[R\mid t]$의 각 성분이 무엇이며 총 DOF는?
- Euclidean / affine / projective 변환이 각각 보존하는 기하 불변량은?
- 왜 단일 이미지로는 3D를 스케일까지 복원할 수 없는가?

{% endraw %}

---

이전: [10. Generative Adversarial Networks](10-generative-adversarial-networks.md) · 다음: [12. Camera Calibration](12-camera-calibration.md)
