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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **00. 강의 개요와 학습 지도**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
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
- **00. 강의 개요와 학습 지도**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

다음: [01. Introduction to Computer Vision & ML](01-introduction.md)
