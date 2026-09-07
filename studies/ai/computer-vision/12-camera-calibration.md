---
layout: page
title: "12. Camera Calibration"
permalink: /studies/ai/computer-vision/12-camera-calibration/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/12%20Camera%20Calibration.md)

{% raw %}
## 한눈에 보기
Camera Calibration 강의는 3D world point와 2D image point 대응으로 camera intrinsic/extrinsic parameter를 추정하는 방법을 다룬다.

## 핵심 개념
- pinhole camera model
- intrinsic parameters
- extrinsic parameters
- DLT
- SVD
- Zhang method
- homography
- calibration object

## 체계적 정리
- pinhole camera model은 world point가 rotation, translation, intrinsic matrix를 거쳐 image point로 projection된다고 본다.
- calibration의 목표는 focal length, principal point, skew 같은 intrinsic과 camera pose에 해당하는 extrinsic을 추정하는 것이다.
- DLT는 point correspondence로 projection matrix를 선형 방정식 $Ap=0$ 형태로 세우고 SVD로 푼다.
- Zhang method는 planar calibration object와 여러 view의 homography를 이용해 intrinsic parameter를 추정한다.
- homography는 한 평면 위 점들의 두 이미지 또는 plane-image mapping을 3x3 projective matrix로 표현한다.

## 중요한 수식과 관점
- Projection: $s x = K [R|t] X$.
- DLT constraint: $A_i p = 0$.
- Planar case: $s x = H X_{plane}$.
- $B=K^{-T}K^{-1}$는 intrinsic constraint를 선형화하는 데 쓰인다.

## 구현과 학습 포인트
calibration 구현에서는 correspondences의 수, 좌표 normalization, SVD solution의 scale ambiguity, reprojection error 평가가 중요하다. Zhang method는 실제 checkerboard calibration의 이론적 기반이다.

## 자주 헷갈리는 지점
- projection matrix는 scale이 임의이므로 같은 matrix에 상수를 곱해도 같은 mapping을 나타낸다.
- homography는 planar scene 또는 pure rotation 상황에서만 2D-2D mapping으로 충분하다.
- calibration 결과는 reprojection error로 검증해야 한다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Geometry 1](11-3d-geometry-1.md)
- [Two-view Geometry 1](13-1-two-view-geometry-1.md)
- [Two-view Geometry 2](13-2-two-view-geometry-2.md)

{% endraw %}

---

이전: [11. Geometry 1](11-3d-geometry-1.md) · 다음: [13. Two-view Geometry 1](13-1-two-view-geometry-1.md)
