---
layout: page
title: "12. Camera Calibration"
permalink: /studies/ai/computer-vision/12-camera-calibration/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `12` 보강
- 표준 교재(Hartley–Zisserman · Zhang 2000) 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

캘리브레이션은 3D 월드 점과 2D 이미지 점의 대응으로 **camera parameter를 추정**한다. intrinsic($$K$$: 초점·주점), extrinsic($$R,t$$: 자세), 그리고 렌즈 왜곡 계수를 구한다.

## 1. 문제 설정

$$
s\,\mathbf{x} = P\,\mathbf{X},\qquad P = K[R\mid t]\ \ (3\times4)
$$

대응점 $$\{(\mathbf{X}_i,\mathbf{x}_i)\}$$가 주어질 때 $$P$$(또는 $$K,R,t$$)를 찾는다.

## 2. DLT (Direct Linear Transform)

각 대응점은 $$P$$의 원소에 대해 2개의 선형식을 준다. $$\mathbf{x}_i \times (P\mathbf{X}_i) = 0$$ 를 전개하면:

$$
A\,\mathbf{p} = 0,\qquad \mathbf{p} = \operatorname{vec}(P)\in\mathbb{R}^{12}
$$

- $$\ge 6$$개 대응점 → $$A$$는 $$2N\times12$$. 자명해를 피하려 $$\lVert\mathbf p\rVert=1$$ 제약.
- 해 = $$A^\top A$$의 **최소 특이값에 대응하는 특이벡터**(즉 $$A$$의 SVD에서 마지막 $$V$$ 열).
- **좌표 정규화**(평균 0, 평균거리 $$\sqrt2$$/ $$\sqrt3$$)가 수치 안정성에 필수.
- 구한 $$P$$에서 $$K,R,t$$는 좌상단 $$3\times3$$의 **RQ 분해**로 분리($$K$$는 상삼각, $$R$$은 직교).

## 3. Zhang's method (평면 타깃)

체스보드 같은 **평면** 패턴을 여러 자세로 촬영.

1. 각 이미지에서 평면↔이미지 **homography** $$H = K[r_1\ r_2\ t]$$ 추정.
2. $$r_1,r_2$$가 정규직교라는 제약 → $$H$$ 열들로 $$K^{-\top}K^{-1}$$에 대한 선형식 2개/이미지.
3. $$\ge 3$$장이면 $$K$$의 5개 파라미터를 선형으로 푼다.
4. $$K$$에서 이미지별 $$R,t$$ 복원.
5. 전체를 **비선형 최소화**(Levenberg–Marquardt)로 정제, 렌즈 왜곡 계수도 함께 추정.

실무 표준(OpenCV `calibrateCamera`가 이 방식). 3D 정밀 타깃이 필요 없어 편리.

## 4. 렌즈 왜곡

$$
x_d = x_u(1 + k_1 r^2 + k_2 r^4 + \dots) + \text{tangential}
$$
방사(radial) 왜곡 $$k_1,k_2,\dots$$, 접선(tangential) $$p_1,p_2$$. 캘리브레이션 후 undistort하면 직선이 직선으로.

## 5. 재투영 오차 (reprojection error)

$$
E = \sum_i \big\lVert \mathbf{x}_i - \pi(P;\mathbf{X}_i) \big\rVert^2
$$
캘리브레이션 품질 지표. 픽셀 단위로 보통 < 1 이면 양호.

## 자주 틀리는 지점

- DLT는 **대수적 오차**를 최소화 → 최종적으로 재투영(기하) 오차로 비선형 정제해야 한다.
- 정규화를 생략하면 $$A$$의 조건수가 나빠 해가 불안정.
- Zhang's method는 **평면** 타깃 + **여러 자세**가 필요(한 자세만으론 부족).
- $$K,R,t$$ 분해 시 $$K$$의 대각 부호를 양수로 맞추는 후처리 필요.
- 왜곡을 무시하면 광각 렌즈에서 큰 오차.

## 복습 질문

- DLT의 선형 시스템 $$A\mathbf p = 0$$이 어떻게 세워지고, 해를 SVD로 어떻게 구하는가?
- 좌표 정규화가 왜 필요한가?
- Zhang's method가 평면 타깃만으로 $$K$$를 구할 수 있는 원리(정규직교 제약)는?
- reprojection error의 정의와, DLT 해를 왜 비선형 정제하는가?

{% endraw %}

---

이전: [11. 3D Geometry](11-3d-geometry.md) · 다음: [13. Two-view Geometry](13-two-view-geometry.md)
