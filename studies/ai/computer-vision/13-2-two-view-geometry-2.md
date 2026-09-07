---
layout: page
title: "13. Two-view Geometry 2"
permalink: /studies/ai/computer-vision/13-2-two-view-geometry-2/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/13-2%20Two-view%20Geometry%202.md)

{% raw %}
## 한눈에 보기
Two-view Geometry 2는 epipolar geometry를 fundamental matrix와 essential matrix로 수식화하고, 대응점에서 camera pose와 3D point를 추정하는 흐름을 다룬다.

## 핵심 개념
- fundamental matrix
- essential matrix
- eight-point algorithm
- rank constraint
- normalization
- triangulation
- bundle adjustment

## 체계적 정리
- Fundamental matrix F는 한 이미지의 점을 다른 이미지의 epipolar line으로 mapping한다.
- epipolar constraint는 $x'^T F x = 0$ 형태로 표현된다.
- eight-point algorithm은 여러 correspondence로 선형 시스템을 만들고 F를 추정한다.
- 추정된 F는 rank 2 constraint를 만족해야 하므로 SVD로 가장 작은 singular value를 0으로 만든다.
- coordinate normalization은 numerical stability를 크게 개선한다.
- calibrated camera에서는 essential matrix $E=K'^T F K$를 사용한다.
- triangulation은 두 view의 ray를 이용해 3D point를 복원하고, bundle adjustment는 camera pose와 3D point를 함께 최적화한다.

## 중요한 수식과 관점
- Epipolar line: $l'=Fx$.
- Epipolar constraint: $x'^T F x=0$.
- Essential matrix: $E=K'^T F K$.
- Bundle adjustment는 reprojection error를 비선형 최소화한다.

## 구현과 학습 포인트
실제 SfM pipeline은 feature detection, matching, RANSAC 기반 F/E 추정, pose recovery, triangulation, bundle adjustment 순서로 이어진다. 이 강의는 그 중 핵심 기하학을 담당한다.

## 자주 헷갈리는 지점
- F는 uncalibrated camera의 image coordinate 관계이고, E는 calibrated normalized camera coordinate 관계다.
- eight-point algorithm은 noise에 민감하므로 normalization과 outlier rejection이 필요하다.
- triangulation만으로 끝내지 않고 bundle adjustment로 전체 reprojection error를 줄여야 한다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Two-view Geometry 1](13-1-two-view-geometry-1.md)
- [Camera Calibration](12-camera-calibration.md)
- [기말고사 리뷰](99-final-exam-review.md)

{% endraw %}

---

이전: [13. Two-view Geometry 1](13-1-two-view-geometry-1.md) · 다음: [25. Gaussian Pyramid 구현 노트](25-gaussian-pyramid-implementation-notes.md)
