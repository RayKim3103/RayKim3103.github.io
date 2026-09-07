---
layout: page
title: "00. 강의 개요와 평가 구조"
permalink: /studies/ai/computer-vision/00-course-overview-and-grading/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/00%20%EA%B0%95%EC%9D%98%20%EA%B0%9C%EC%9A%94%EC%99%80%20%ED%8F%89%EA%B0%80%20%EA%B5%AC%EC%A1%B0.md)

{% raw %}
## 한눈에 보기
Computer Vision 과목 전체의 범위, 선수 지식, 평가 비중, 주요 주제를 정리한 강의 계획 노트이다. 2D image processing에서 CNN, transformer, generative model, 3D geometry까지 이어지는 큰 지도를 먼저 잡는다.

## 핵심 개념
- Computer Vision
- image processing
- CNN
- 3D vision
- generative models
- assignment
- midterm
- final

## 체계적 정리
- 과목은 이미지와 비디오를 컴퓨터가 이해하도록 만드는 알고리즘을 다룬다. 초반에는 영상 형성, 색, filtering, edge, pyramid 같은 전통적 처리 기법을 배운다.
- 중반에는 classification, linear model, neural network, CNN architecture, normalization, residual learning을 다룬다.
- 후반에는 detection, segmentation, transformer, VAE, GAN, diffusion 계열 generative model, 3D geometry, camera calibration, two-view geometry가 이어진다.
- 평가는 출석, 과제, 중간/기말고사로 구성되며, 수학과 프로그래밍 실습을 모두 요구한다.

## 중요한 수식과 관점
- 이미지는 matrix 또는 tensor이며, 대부분의 CV 문제는 이 tensor에서 의미 있는 구조를 추정하는 문제로 볼 수 있다.
- 2D vision은 pixel space와 feature space를, 3D vision은 projective geometry와 camera model을 함께 사용한다.

## 구현과 학습 포인트
강의 전체를 복습할 때는 `image formation - low-level processing - recognition - generation - 3D reconstruction` 순서로 연결하면 흐름이 잘 보인다. 각 주제는 독립적 암기보다 어떤 입력을 어떤 표현으로 바꾸는지에 초점을 맞춰 읽는 것이 좋다.

## 자주 헷갈리는 지점
- CV는 단순히 딥러닝만의 과목이 아니다. projection, sampling, filtering, optimization 같은 고전적 기반이 뒤쪽 모델 해석에도 계속 쓰인다.
- 과제 자료와 강의 PDF가 같은 주제를 서로 보완할 수 있으므로 실습 노트도 함께 봐야 한다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Introduction to Computer Vision and Machine Learning](01-introduction.md)
- [Image Processing 통합 개요](02-image-processing-overview.md)
- [기말고사 리뷰](99-final-exam-review.md)

{% endraw %}

---

다음: [01. Introduction to Computer Vision and Machine Learning](01-introduction.md)
