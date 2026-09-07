---
layout: page
title: "01. Introduction to Computer Vision and Machine Learning"
permalink: /studies/ai/computer-vision/01-introduction/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/01%20Introduction%20to%20Computer%20Vision%20and%20Machine%20Learning.md)

{% raw %}
## 한눈에 보기
컴퓨터 비전과 머신러닝의 역사적 배경, 대표 task, 딥러닝 폭발 이후의 변화를 소개하는 자료이다. 사람이 이미지를 이해하는 것처럼 컴퓨터가 pixel에서 semantic information을 추론하는 과정을 큰 틀에서 설명한다.

## 핵심 개념
- AI history
- computer vision tasks
- ImageNet
- classification
- localization
- detection
- segmentation
- machine learning

## 체계적 정리
- 초기 vision 연구는 3D 구조 추론과 edge/shape 분석에서 출발했다.
- ImageNet과 대규모 dataset은 deep learning 기반 recognition 성능 도약의 핵심 배경이 되었다.
- classification은 이미지 전체 label을 예측하고, localization은 객체 위치를 함께 찾으며, detection은 여러 객체의 class와 box를 출력한다.
- segmentation은 pixel 단위 class 또는 instance mask를 예측한다.
- 딥러닝 이후 vision 모델은 image retrieval, video classification, pose recognition, game playing, generation 등으로 확장되었다.

## 중요한 수식과 관점
- 지도학습은 $P(Y|X)$ 또는 decision boundary를 학습하는 관점으로 볼 수 있다.
- 데이터, 모델, loss, optimization, evaluation metric이 하나의 pipeline을 이룬다.

## 구현과 학습 포인트
이 노트는 이후 classification, CNN, detection, segmentation의 언어를 잡는 역할을 한다. 각 task의 output format을 명확히 구분하면 뒤쪽 모델 구조가 왜 달라지는지 이해하기 쉽다.

## 자주 헷갈리는 지점
- classification과 detection은 모두 class를 다루지만 출력 구조가 다르다.
- ImageNet 성능 향상은 architecture만의 결과가 아니라 dataset 규모, GPU, augmentation, optimization 발전이 함께 만든 결과다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Introduction to Computer Vision and Machine Learning 개정판](01r-introduction-revised.md)
- [Classification 통합](03-classification-overview.md)
- [Detection and Segmentation](06-detection-and-segmentation.md)

{% endraw %}

---

이전: [00. 강의 개요와 평가 구조](00-course-overview-and-grading.md) · 다음: [01. Introduction to Computer Vision and Machine Learning 개정판](01r-introduction-revised.md)
