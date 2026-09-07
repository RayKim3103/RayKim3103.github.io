---
layout: page
title: "01. Introduction to Computer Vision and Machine Learning 개정판"
permalink: /studies/ai/computer-vision/01r-introduction-revised/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/01R%20Introduction%20to%20Computer%20Vision%20and%20Machine%20Learning%20%EA%B0%9C%EC%A0%95%ED%8C%90.md)

{% raw %}
## 한눈에 보기
1강 개정판은 기본 소개에서 더 나아가 최근 생성형 AI, multimodal model, video generation, LLM 성능 흐름까지 포함해 컴퓨터 비전의 현재 위치를 보여준다.

## 핵심 개념
- AI
- deep learning explosion
- multimodal AI
- data intelligence
- computer vision applications

## 체계적 정리
- Computer Vision은 image와 video data를 이해하고 조작하는 AI의 핵심 분야로 소개된다.
- 2012년 이후 deep learning은 classification뿐 아니라 segmentation, object detection, activity recognition 등 대부분의 visual task를 장악했다.
- 최근에는 Midjourney, Kling 같은 image/video generation 사례가 vision의 범위를 recognition에서 generation으로 넓혔다.
- 일반적인 AI pipeline은 data에서 feature와 representation을 얻고, 이를 decision 또는 생성 결과로 바꾸는 과정이다.

## 중요한 수식과 관점
- representation learning은 raw input을 task에 유용한 feature space로 바꾸는 과정이다.
- multimodal model은 image, video, text, speech를 같은 semantic space에서 연결하려는 방향으로 발전한다.

## 구현과 학습 포인트
개정판은 최신 예시가 많으므로 시험 복습에서는 원본 1강의 기본 task 정의와 함께 읽는 것이 좋다. 특히 recognition model과 generation model이 같은 representation learning 기반 위에 있다는 점을 연결해 둔다.

## 자주 헷갈리는 지점
- 최신 예시는 기술 흐름을 이해하기 위한 것이며, 수식 중심 시험 대비는 classification과 neural network 강의에서 보강해야 한다.
- 생성형 AI가 등장해도 sampling, likelihood, adversarial learning 같은 기본 개념이 여전히 중요하다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Introduction to Computer Vision and Machine Learning](01-introduction.md)
- [Generative Models](08-generative-models.md)
- [Generative Adversarial Networks](10-generative-adversarial-networks.md)

{% endraw %}

---

이전: [01. Introduction to Computer Vision and Machine Learning](01-introduction.md) · 다음: [02. Image Processing 통합 개요](02-image-processing-overview.md)
