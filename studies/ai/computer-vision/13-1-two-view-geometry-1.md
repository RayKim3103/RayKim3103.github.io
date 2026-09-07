---
layout: page
title: "13. Two-view Geometry 1"
permalink: /studies/ai/computer-vision/13-1-two-view-geometry-1/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/13-1%20Two-view%20Geometry%201.md)

{% raw %}
## 한눈에 보기
Two-view Geometry 1은 두 카메라가 같은 3D point를 볼 때 image points 사이에 생기는 epipolar constraint를 직관적으로 소개한다.

## 핵심 개념
- epipolar geometry
- epipole
- epipolar line
- epipolar plane
- two-view correspondence
- stereo

## 체계적 정리
- 3D point X와 두 camera center가 만드는 평면이 epipolar plane이다.
- epipolar plane이 각 image plane과 만나는 선이 epipolar line이다.
- 한 이미지의 점 x가 주어지면, 다른 이미지의 대응점 x'는 전체 2D plane이 아니라 특정 epipolar line 위에 있어야 한다.
- epipole은 한 camera center가 다른 camera image plane에 투영된 점이다.
- converging cameras와 forward motion에서는 epipolar line 패턴이 다르게 나타난다.

## 중요한 수식과 관점
- Epipolar constraint는 대응점 탐색을 2D search에서 1D line search로 줄인다.
- 점 x에 대응하는 다른 이미지의 epipolar line은 fundamental matrix로 표현된다.
- stereo geometry는 depth estimation과 3D reconstruction의 기초다.

## 구현과 학습 포인트
이 자료는 직관 중심이다. epipolar line이 왜 생기는지 그림으로 이해한 뒤, 13-2에서 fundamental matrix와 eight-point algorithm으로 수식화하면 좋다.

## 추가 해석 포인트
- epipolar geometry는 두 이미지 사이의 대응점 탐색 범위를 크게 줄인다. 첫 번째 이미지의 한 점이 정해지면 두 번째 이미지에서는 가능한 위치가 epipolar line 위로 제한된다.
- epipole은 다른 카메라의 중심이 현재 이미지에 투영된 점이다. 카메라가 앞으로 움직이는 경우 epipolar line이 epipole을 중심으로 방사형으로 나타나는 이유가 여기에 있다.
- 이 단계에서는 아직 depth를 직접 계산하지 않는다. 대응점과 camera geometry가 갖는 제약을 먼저 세우고, triangulation은 그 다음 단계에서 수행한다.

## 자주 헷갈리는 지점
- epipolar line은 두 이미지에서 쌍으로 존재한다.
- forward motion에서는 epipolar line이 epipole에서 방사형으로 퍼지는 형태가 된다.
- 대응점은 epipolar line 위에 있어야 하지만, line 위의 어느 점인지는 추가 matching이 필요하다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Two-view Geometry 2](13-2-two-view-geometry-2.md)
- [Camera Calibration](12-camera-calibration.md)
- [Geometry 1](11-3d-geometry-1.md)

{% endraw %}

---

이전: [12. Camera Calibration](12-camera-calibration.md) · 다음: [13. Two-view Geometry 2](13-2-two-view-geometry-2.md)
