---
layout: page
title: "11. Geometry 1"
permalink: /studies/ai/computer-vision/11-3d-geometry-1/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/11%203D%20Geometry%201.md)

{% raw %}
## 한눈에 보기
3D Geometry 1은 2D image와 3D world의 관계를 projective geometry로 설명하는 자료이다. camera model, homogeneous coordinate, transformation hierarchy가 핵심이다.

## 핵심 개념
- 3D reconstruction
- pinhole camera
- Euclidean space
- projective space
- homogeneous coordinates
- translation
- rotation
- rigid transform

## 체계적 정리
- 3D reconstruction은 여러 이미지에서 scene geometry와 camera pose를 추정하는 문제다.
- pinhole camera는 3D point를 image plane으로 projection하는 기본 모델이다.
- Euclidean space는 거리와 각도가 의미 있는 좌표 공간이고, projective space는 homogeneous coordinate로 무한원점과 projection을 다룬다.
- homogeneous coordinate를 쓰면 translation, rotation, projection을 matrix form으로 통합할 수 있다.
- transformation hierarchy는 Euclidean, similarity, affine, projective transformation으로 갈수록 보존되는 기하 성질이 줄어든다.

## 중요한 수식과 관점
- 3D homogeneous point: $X=[X,Y,Z,1]^T$.
- 2D homogeneous point: $x=[u,v,1]^T$.
- Rigid transform: $X'=RX+t$.

## 구현과 학습 포인트
이 노트는 camera calibration과 two-view geometry의 필수 기반이다. 점, 선, 평면을 homogeneous coordinate로 표현하고, matrix multiplication으로 좌표계를 바꾸는 데 익숙해져야 한다.

## 자주 헷갈리는 지점
- homogeneous coordinate는 마지막 성분을 나누어 Euclidean coordinate로 돌아온다.
- projective transform에서는 길이와 각도가 보존되지 않을 수 있다.
- camera coordinate와 world coordinate를 구분하지 않으면 extrinsic matrix 해석이 꼬인다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Camera Calibration](12-camera-calibration.md)
- [Two-view Geometry 1](13-1-two-view-geometry-1.md)
- [Image Processing 통합 개요](02-image-processing-overview.md)

{% endraw %}

---

이전: [10. Generative Adversarial Networks](10-generative-adversarial-networks.md) · 다음: [12. Camera Calibration](12-camera-calibration.md)
