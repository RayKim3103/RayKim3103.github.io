---
layout: page
title: "01. Introduction to Computer Vision & Machine Learning"
permalink: /studies/ai/computer-vision/01-introduction/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `01` + `01R(개정판)` 통합·보강
- 표준 CV 교재(CS231n · Szeliski) 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

컴퓨터 비전은 pixel 배열에서 **semantic 정보**(무엇이 / 어디에 / 어떤 형태로 있는가)를 추론하는 분야다. 이 강의는 (1) 비전 문제의 역사와 대표 task, (2) 2012년 딥러닝 도약의 배경, (3) recognition을 넘어 generation·multimodal로 넓어진 현재 지형을 훑는다.

## 1. 비전 문제의 난이도

같은 3D 장면이 조명, 시점, 가림(occlusion), 배경, 클래스 내 변이(intra-class variation)에 따라 완전히 다른 pixel 값을 만든다. 규칙 기반으로 "고양이 = ...")을 적기가 불가능하므로, **데이터에서 함수를 학습**한다. 이것이 CV가 곧 ML 문제가 되는 이유다.

## 2. 대표 task와 출력 형식

| task | 출력 | 예 |
|---|---|---|
| **classification** | 이미지 1개당 클래스 1개 | "이 사진은 개" |
| **classification + localization** | 클래스 + 박스 1개 | 단일 객체 위치 |
| **object detection** | (클래스, 박스) 여러 개 | 거리 사진의 모든 차/사람 |
| **semantic segmentation** | pixel마다 클래스 | 도로/하늘/건물 마스크 |
| **instance segmentation** | pixel마다 (클래스, 객체 id) | 사람 1, 사람 2 분리 |
| **generation** | 새 이미지/비디오 | text-to-image |

> 뒤에 나오는 모델 구조(FCN, R-CNN, ViT, GAN…)의 차이는 대부분 **이 출력 형식**에서 비롯된다.

## 3. 학습 pipeline

$$
\text{data} \xrightarrow{\text{feature / representation}} h(x;\theta) \xrightarrow{\text{loss } L} \text{gradient} \xrightarrow{\text{optimizer}} \theta' \xrightarrow{\text{metric}} \text{평가}
$$

- **표현 학습(representation learning)**: raw pixel을 task에 유용한 feature 공간으로 사상. 딥러닝의 핵심 이점은 feature를 사람이 설계하지 않고 end-to-end로 학습한다는 것.
- **지도학습 관점**: 분류는 $P(Y \mid X)$ 또는 결정경계를 학습하는 문제.

## 4. 딥러닝 도약(2012~)의 3요소

1. **대규모 데이터셋** — ImageNet(1000 클래스, ~1.2M 이미지)이 벤치마크를 정의.
2. **연산** — GPU로 대형 CNN 학습이 현실화(AlexNet).
3. **알고리즘** — ReLU, dropout, batch norm, 더 나은 초기화·optimizer, data augmentation.

AlexNet 이후 classification뿐 아니라 detection, segmentation, pose, video, RL, 그리고 generation까지 딥러닝이 표준이 됐다. 성능 향상은 architecture 하나의 공로가 아니라 위 요소들의 합작이다.

## 5. 현재 지형 (개정판 추가분)

- **생성형 AI**: Midjourney·Kling 등으로 비전의 범위가 recognition → generation 으로 확장.
- **Multimodal**: image·video·text·speech를 같은 semantic 공간에서 연결(CLIP류, VLM).
- recognition model과 generation model은 모두 **representation learning** 위에 서 있다. 생성형이 등장해도 sampling, likelihood, adversarial learning 같은 기본 개념은 그대로 중요하다.

## 자주 틀리는 지점

- classification과 detection은 둘 다 "클래스"를 다루지만 **출력 텐서 shape**가 다르다(스칼라 vs 가변 길이 리스트).
- "ImageNet 정확도가 올랐다" = architecture만 좋아진 게 아니라 augmentation·optimizer·정규화가 함께 발전한 결과.
- 최신 생성 사례는 흐름 이해용이고, 수식 중심 시험 대비는 classification·neural network 파트에서 해야 한다.

## 복습 질문

- 6가지 대표 task를 출력 형식으로 구분해 설명할 수 있는가?
- 딥러닝 도약의 3요소를 각각 왜 필요한지와 함께 말할 수 있는가?
- representation learning이 recognition과 generation을 어떻게 잇는가?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **01. Introduction to Computer Vision & Machine Learning**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

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
- **01. Introduction to Computer Vision & Machine Learning**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [00. 강의 개요와 학습 지도](00-course-overview.md) · 다음: [02. Image Processing](02-image-processing.md)
