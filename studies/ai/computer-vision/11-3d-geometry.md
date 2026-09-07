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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **11. 3D Geometry**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
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
- **11. 3D Geometry**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [10. Generative Adversarial Networks](10-generative-adversarial-networks.md) · 다음: [12. Camera Calibration](12-camera-calibration.md)
