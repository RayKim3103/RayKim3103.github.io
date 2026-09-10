---
layout: page
title: "13. Two-view Geometry"
permalink: /studies/ai/computer-vision/13-two-view-geometry/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `13-1(직관)` + `13-2(수식)` 통합·보강
- 표준 교재(Hartley–Zisserman) 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

두 카메라가 같은 3D 점을 볼 때 두 이미지 점 사이에는 **epipolar 제약**이 성립한다. 이를 fundamental / essential matrix로 수식화하면, 대응점만으로 상대 카메라 자세와 3D 구조를 복원할 수 있다(Structure from Motion의 핵심).

## 1. Epipolar geometry (직관)

- **baseline**: 두 카메라 중심 $$C, C'$$를 잇는 선.
- **epipole** $$e, e'$$: baseline이 각 이미지 평면과 만나는 점(= 다른 카메라 중심의 상).
- **epipolar plane**: 3D 점 $$X$$와 $$C, C'$$가 이루는 평면.
- **epipolar line** $$l'$$: epipolar plane이 두 번째 이미지와 만나는 선. 첫 이미지의 점 $$x$$에 대응하는 점 $$x'$$는 **반드시 $$l'$$ 위**에 있다.

→ 대응점 탐색이 2D 문제에서 **1D(선 위)** 문제로 줄어든다(스테레오 매칭의 근거).

## 2. Fundamental matrix $$F$$

이미지 좌표(캘리브레이션 불필요)에서:

$$
\boxed{\;x'^{\top} F\, x = 0\;}\qquad
l' = F x,\quad l = F^{\top} x'
$$

- $$F$$는 $$3\times3$$, **rank 2**, 스케일 자유도 → **7 DOF**.
- $$F e = 0$$, $$F^{\top} e' = 0$$ (epipole은 영공간).

## 3. Essential matrix $$E$$

캘리브레이션된(정규화 좌표 $$\hat x = K^{-1}x$$) 경우:

$$
\hat x'^{\top} E\, \hat x = 0,\qquad
E = K'^{\top} F K = [t]_\times R
$$

- $$E$$는 **5 DOF**(회전 3 + 이동 방향 2; 이동 크기는 미지).
- 특이값이 $$(\sigma,\sigma,0)$$ 형태여야 함.
- $$E$$를 SVD하면 $$R$$(4가지 후보)와 $$t$$(부호 2가지)를 얻고, **삼각측량 후 점이 두 카메라 앞에 있는** 해 하나를 고른다(cheirality check).

## 4. Eight-point algorithm ($$F$$ 추정)

1. 각 대응점 $$x'^\top F x = 0$$ → $$F$$의 9원소에 대한 선형식 1개. $$\ge 8$$점 → $$Af = 0$$.
2. SVD로 $$\lVert f\rVert=1$$ 최소자승 해.
3. **rank-2 강제**: 얻은 $$\hat F$$를 SVD $$U\Sigma V^\top$$, $$\Sigma = \operatorname{diag}(\sigma_1,\sigma_2,\sigma_3)$$에서 $$\sigma_3\!\gets\!0$$ 후 재조립.
4. **정규화된 8점 알고리즘**: 좌표를 평균 0·평균거리 $$\sqrt2$$로 정규화하면 조건수가 크게 개선(필수).
5. 노이즈/이상치가 있으면 **RANSAC**으로 $$F$$/$$E$$ 추정.

## 5. 3D 복원

- **triangulation**: 두 카메라의 광선 교점으로 $$X$$ 복원(노이즈로 정확히 안 만나므로 최소자승 / 중점 / DLT).
- **bundle adjustment**: 모든 카메라 자세와 3D 점을 함께 놓고 **재투영 오차**를 비선형 최소화. SfM/SLAM의 최종 정제 단계.

전형적 SfM 파이프라인:
```
feature detection → matching → (RANSAC) F/E 추정 → pose recovery
        → triangulation → bundle adjustment
```

## 자주 틀리는 지점

- **$$F$$ vs $$E$$**: $$F$$는 uncalibrated 이미지 좌표 관계(7 DOF), $$E$$는 calibrated 정규화 좌표 관계(5 DOF), $$E = K'^\top F K$$.
- **$$F$$ vs homography $$H$$**: $$H$$는 점→점(평면/순수회전), $$F$$는 점→선(일반 장면).
- rank-2 강제와 좌표 정규화를 빼면 8점 알고리즘이 노이즈에 매우 취약.
- $$E$$ 분해는 4개 해가 나오며 cheirality로 하나를 골라야 한다.
- triangulation으로 끝내지 말고 bundle adjustment로 전역 오차를 줄인다.

## 복습 질문

- epipole, epipolar line, epipolar plane을 정의하고, 대응점 탐색이 왜 1D 문제가 되는지 설명하라.
- $$x'^\top F x = 0$$의 의미와 $$F$$의 rank·DOF는?
- $$E = K'^\top F K$$를 유도하고 $$E$$의 DOF가 5인 이유는?
- 정규화된 8점 알고리즘의 단계와 rank-2 강제의 이유는?

{% endraw %}

---

이전: [12. Camera Calibration](12-camera-calibration.md) · 다음: [25. Gaussian Pyramid (project)](25-gaussian-pyramid-project.md)
