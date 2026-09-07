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

- **baseline**: 두 카메라 중심 $C, C'$를 잇는 선.
- **epipole** $e, e'$: baseline이 각 이미지 평면과 만나는 점(= 다른 카메라 중심의 상).
- **epipolar plane**: 3D 점 $X$와 $C, C'$가 이루는 평면.
- **epipolar line** $l'$: epipolar plane이 두 번째 이미지와 만나는 선. 첫 이미지의 점 $x$에 대응하는 점 $x'$는 **반드시 $l'$ 위**에 있다.

→ 대응점 탐색이 2D 문제에서 **1D(선 위)** 문제로 줄어든다(스테레오 매칭의 근거).

## 2. Fundamental matrix $F$

이미지 좌표(캘리브레이션 불필요)에서:

$$
\boxed{\;x'^{\top} F\, x = 0\;}\qquad
l' = F x,\quad l = F^{\top} x'
$$

- $F$는 $3\times3$, **rank 2**, 스케일 자유도 → **7 DOF**.
- $F e = 0$, $F^{\top} e' = 0$ (epipole은 영공간).

## 3. Essential matrix $E$

캘리브레이션된(정규화 좌표 $\hat x = K^{-1}x$) 경우:

$$
\hat x'^{\top} E\, \hat x = 0,\qquad
E = K'^{\top} F K = [t]_\times R
$$

- $E$는 **5 DOF**(회전 3 + 이동 방향 2; 이동 크기는 미지).
- 특이값이 $(\sigma,\sigma,0)$ 형태여야 함.
- $E$를 SVD하면 $R$(4가지 후보)와 $t$(부호 2가지)를 얻고, **삼각측량 후 점이 두 카메라 앞에 있는** 해 하나를 고른다(cheirality check).

## 4. Eight-point algorithm ($F$ 추정)

1. 각 대응점 $x'^\top F x = 0$ → $F$의 9원소에 대한 선형식 1개. $\ge 8$점 → $Af = 0$.
2. SVD로 $\lVert f\rVert=1$ 최소자승 해.
3. **rank-2 강제**: 얻은 $\hat F$를 SVD $U\Sigma V^\top$, $\Sigma = \operatorname{diag}(\sigma_1,\sigma_2,\sigma_3)$에서 $\sigma_3\!\gets\!0$ 후 재조립.
4. **정규화된 8점 알고리즘**: 좌표를 평균 0·평균거리 $\sqrt2$로 정규화하면 조건수가 크게 개선(필수).
5. 노이즈/이상치가 있으면 **RANSAC**으로 $F$/$E$ 추정.

## 5. 3D 복원

- **triangulation**: 두 카메라의 광선 교점으로 $X$ 복원(노이즈로 정확히 안 만나므로 최소자승 / 중점 / DLT).
- **bundle adjustment**: 모든 카메라 자세와 3D 점을 함께 놓고 **재투영 오차**를 비선형 최소화. SfM/SLAM의 최종 정제 단계.

전형적 SfM 파이프라인:
```
feature detection → matching → (RANSAC) F/E 추정 → pose recovery
        → triangulation → bundle adjustment
```

## 자주 틀리는 지점

- **$F$ vs $E$**: $F$는 uncalibrated 이미지 좌표 관계(7 DOF), $E$는 calibrated 정규화 좌표 관계(5 DOF), $E = K'^\top F K$.
- **$F$ vs homography $H$**: $H$는 점→점(평면/순수회전), $F$는 점→선(일반 장면).
- rank-2 강제와 좌표 정규화를 빼면 8점 알고리즘이 노이즈에 매우 취약.
- $E$ 분해는 4개 해가 나오며 cheirality로 하나를 골라야 한다.
- triangulation으로 끝내지 말고 bundle adjustment로 전역 오차를 줄인다.

## 복습 질문

- epipole, epipolar line, epipolar plane을 정의하고, 대응점 탐색이 왜 1D 문제가 되는지 설명하라.
- $x'^\top F x = 0$의 의미와 $F$의 rank·DOF는?
- $E = K'^\top F K$를 유도하고 $E$의 DOF가 5인 이유는?
- 정규화된 8점 알고리즘의 단계와 rank-2 강제의 이유는?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **13. Two-view Geometry**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
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
- **13. Two-view Geometry**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [12. Camera Calibration](12-camera-calibration.md) · 다음: [25. Gaussian Pyramid (project)](25-gaussian-pyramid-project.md)
