---
layout: page
title: "00. 강의 개요와 학습 지도"
permalink: /studies/ai/computer-vision/00-course-overview/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `00` 정리
- 강의 슬라이드의 표기·평가 비중과 대조해 사용하세요.

{% raw %}
## 개요

Computer Vision은 이미지·비디오라는 고차원 tensor에서 **의미 있는 구조**(라벨, 위치, 마스크, 3D 형상, 새 이미지)를 추정하는 알고리즘을 다룬다. 이 과목은 크게 네 층으로 쌓인다.

```
image formation ──► low-level processing ──► recognition ──► generation
        └──────────────────────────────────► 3D reconstruction
```

## 전체 지도

| 파트 | 다루는 것 | 핵심 관점 |
|---|---|---|
| 영상 형성·저수준 처리 | pinhole projection, 색, filtering, edge, morphology, sampling/aliasing, pyramid | 이미지는 연속 신호를 이산 격자에 sampling한 결과. 주파수·선형 시스템 관점 |
| 인식 (얕은 모델) | linear/logistic/softmax classifier, regularization, bias–variance, 평가지표 | 데이터→feature→결정경계. 일반화가 목표 |
| 신경망·CNN | MLP·역전파, convolution, normalization, VGG/ResNet | 깊은 네트워크를 안정적으로 학습시키는 장치들 |
| 밀집 예측 | semantic/instance segmentation, object detection | image-level → pixel/object-level 로 출력 구조 확장 |
| Attention | self-attention, Transformer, ViT | inductive bias를 데이터로 대체 |
| 생성 모델 | autoregressive, VAE/VQ-VAE/VQGAN, GAN, diffusion | likelihood vs adversarial, sampling 속도 trade-off |
| 3D 비전 | projective geometry, camera calibration, epipolar geometry, triangulation | 2D 관측 ↔ 3D 세계의 기하 관계 |

## 학습 전략

- 각 주제를 **입력 / 출력 / 목적함수(또는 기하 대상)** 세 가지로 먼저 요약한다. 뒤쪽 모델 구조가 왜 달라지는지는 대부분 "출력 형식이 달라서"이다.
- 딥러닝 파트에서도 projection, sampling, filtering, convex optimization 같은 고전 기반이 계속 재등장한다. CV는 딥러닝만의 과목이 아니다.
- 강의 PDF와 과제(ipynb) 자료는 같은 주제를 상호 보완한다. 구현 노트도 함께 본다.

## 선수 지식 체크리스트

- 선형대수: 행렬 곱, 고유값/특이값 분해(SVD), 최소자승
- 확률: 조건부확률, Bayes rule, Gaussian, MLE/MAP
- 미적분: gradient, chain rule
- 프로그래밍: NumPy tensor 조작, PyTorch 학습 루프, `train()`/`eval()` 모드 구분

## 복습 질문

- `image formation → processing → recognition → generation → 3D` 순서로 각 파트의 대표 입출력을 한 문장씩 말할 수 있는가?
- 2D 비전과 3D 비전은 각각 어떤 수학 도구(선형대수/확률/projective geometry)에 더 의존하는가?
- 같은 문제를 전통적 방법과 딥러닝으로 풀 때 바뀌는 가정은 무엇인가?

{% endraw %}

---

다음: [01. Introduction to Computer Vision & ML](01-introduction.md)
