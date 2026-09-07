---
layout: page
title: "02. Image Processing 통합 개요"
permalink: /studies/ai/computer-vision/02-image-processing-overview/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/02%20Image%20Processing%20%ED%86%B5%ED%95%A9%20%EA%B0%9C%EC%9A%94.md)

{% raw %}
## 한눈에 보기
영상 형성, pinhole projection, 색 지각, 이미지 tensor 표현, 기본 변환과 filtering을 통합적으로 다루는 Image Processing 입문 자료이다.

## 핵심 개념
- image formation
- camera obscura
- projection
- intrinsic matrix
- color
- image transformations
- filtering

## 체계적 정리
- 카메라와 눈은 3D 세계의 빛을 2D sensor 또는 retina에 투영한다.
- Perspective projection은 3D point가 camera center를 지나 image plane에 맺히는 기하학으로 설명된다.
- Intrinsic matrix는 focal length, principal point, skew 등 camera 내부 파라미터를 담는다.
- 디지털 이미지는 pixel intensity의 배열이며 grayscale은 2D matrix, RGB image는 3D tensor로 볼 수 있다.
- 색은 cone response와 color space 관점에서 이해할 수 있고, processing 단계에서는 channel별 연산과 색공간 변환이 중요하다.

## 중요한 수식과 관점
- Pinhole projection: $x=fX/Z$, $y=fY/Z$.
- Homogeneous coordinate를 쓰면 perspective projection을 matrix multiplication으로 표현할 수 있다.
- Image transformation은 좌표 mapping과 interpolation 문제를 동반한다.

## 구현과 학습 포인트
이 자료는 이후 camera calibration과 two-view geometry의 전단계다. image processing을 단순 pixel 조작으로 보지 말고, imaging geometry와 sampling이 결합된 신호 처리 문제로 이해해야 한다.

## 자주 헷갈리는 지점
- 3D 직선이 2D 직선으로 투영되더라도 길이와 각도는 일반적으로 보존되지 않는다.
- intrinsic parameter와 extrinsic parameter를 섞어 이해하면 calibration 식을 세울 때 혼란이 생긴다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Image Processing 1 개정판](02-1-image-processing-1-revised.md)
- [Image Processing 2](02-2-image-processing-2.md)
- [Geometry 1](11-3d-geometry-1.md)

{% endraw %}

---

이전: [01. Introduction to Computer Vision and Machine Learning 개정판](01r-introduction-revised.md) · 다음: [02. Image Processing 1 개정판](02-1-image-processing-1-revised.md)
