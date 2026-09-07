---
layout: page
title: "01. 머신러닝 개요"
permalink: /studies/ai/machine-learning/01-introduction/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/01%20Introduction%20-%20%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D%20%EA%B0%9C%EC%9A%94.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #overview #regression #classification #clustering

관련 노트: [점추정과 Maximum Likelihood Estimation](02-point-estimation-mle.md)

## 핵심 요약

이 강의는 인공지능과 머신러닝의 역사, 머신러닝의 정의, 대표 문제 유형을 소개한다. 핵심 메시지는 컴퓨터가 명시적으로 모든 규칙을 프로그래밍받는 대신, 데이터에서 패턴을 학습해 예측이나 의사결정을 수행한다는 것이다.

## 역사적 흐름

| 시기 | 사건 | 의미 |
|---|---|---|
| 1936 | Turing machine | 계산 가능성과 알고리즘의 이론적 기반 |
| 1950 | Turing test | 기계 지능을 판별하는 사고 실험 |
| 1956 | Dartmouth conference | Artificial Intelligence라는 분야의 출발점 |
| 1957 | Perceptron | 초기 신경망 모델 |
| 1969-1985 | AI winter | perceptron 한계와 기대 하락 |
| 1986 이후 | MLP와 backpropagation | 다층 신경망 학습의 재부상 |
| 2006 이후 | Deep learning | 깊은 신경망과 대규모 데이터의 결합 |
| 2012 이후 | AlexNet, ImageNet | 현대 deep learning 폭발의 계기 |

## 머신러닝의 정의

머신러닝은 경험, 즉 데이터를 통해 성능이 개선되는 알고리즘을 연구하는 분야이다. 더 실용적으로는 이미지, 비디오, 텍스트, 음성, 센서 데이터 같은 입력에서 예측값이나 결정을 만들어내는 방법이다.

```text
Data -> Learning algorithm -> Intelligence
```

여기서 intelligence는 규칙 기반 프로그램이라기보다 학습된 함수, 분류기, 검색 모델, 군집 구조 등을 의미한다.

## 머신러닝 파이프라인

1. 데이터를 수집한다.
2. 입력 `x`와 출력 `y`를 정의한다.
3. 모델 구조를 정한다.
4. 학습 알고리즘으로 모델 파라미터를 추정한다.
5. 새로운 데이터에 대해 예측이나 결정을 수행한다.
6. 성능을 평가하고 개선한다.

## 대표 문제 유형

### Regression

Regression은 출력 `y`가 연속값인 문제이다.

예:

- 집 면적, 위치, 방 개수로 집값 예측
- 주식 가격 예측
- 트윗이 얼마나 많이 retweet될지 예측
- 뇌 영역 intensity로 연속적인 인지 상태 예측

핵심 질문은 `x`가 주어졌을 때 실수값 `y`를 얼마나 정확히 예측할 수 있는가이다.

### Classification

Classification은 출력 `y`가 범주형 label인 문제이다.

예:

- 이메일을 spam/not spam으로 분류
- 이미지를 고양이, 자동차, 사람 등으로 분류
- 의료 진단에서 질병 종류 예측
- 감성 분석에서 문장을 positive/negative로 분류

출력 범주가 두 개이면 binary classification, 세 개 이상이면 multiclass classification이다.

### Retrieval

Retrieval은 query와 관련 있는 item을 찾는 문제이다. 문서 검색에서는 문서를 similarity space에 배치하고 query와 가까운 문서를 반환한다.

예:

- 검색 엔진
- 유사 이미지 검색
- 뉴스 기사 추천
- 문서 집합에서 관련 문서 찾기

### Clustering

Clustering은 label 없이 데이터 내부의 유사한 그룹을 찾는 비지도학습 문제이다.

예:

- 이미지를 바다, 산, 도시처럼 시각적으로 유사한 그룹으로 묶기
- 웹사이트 사용자를 행동 패턴별로 묶기
- 문서를 주제별로 자동 구조화하기

### Embedding과 표현 학습

Embedding은 복잡한 데이터를 similarity가 보존되는 vector space로 옮기는 표현이다. 이미지나 문서가 embedding 공간에서 가까우면 의미적으로도 비슷하다고 해석할 수 있다.

## Deep Learning의 확산

2012년 이후 deep learning은 image classification, object detection, segmentation, video classification, activity recognition, pose estimation, reinforcement learning, text-to-image/video generation 등으로 확장되었다. 대규모 데이터, GPU 연산 성능, 알고리즘 개선이 함께 작용했다.

## 시험ㆍ복습 체크포인트

- AI와 ML의 관계를 설명할 수 있어야 한다.
- Regression, classification, retrieval, clustering의 차이를 예시와 함께 구분할 수 있어야 한다.
- `Data -> Intelligence` 관점에서 학습 문제를 정의할 수 있어야 한다.
- ImageNet과 AlexNet이 deep learning 확산에 중요한 이유를 말할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **01. 머신러닝 개요**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 모델의 수식은 외울 식이 아니라 어떤 확률적 가정이나 기하학적 가정에서 나왔는지 함께 보아야 한다.
- training error와 validation/test 성능이 달라지는 이유를 bias, variance, regularization으로 연결한다.
- 분류와 회귀는 모두 feature representation, objective, optimization, evaluation의 선택 문제로 정리할 수 있다.

### 문제 풀이 또는 구현 루틴

- 입력/출력, label의 의미, loss가 벌주는 오류의 종류를 먼저 적는다.
- 수식 전개에서는 차원을 확인하고 scalar objective와 vector/matrix gradient가 맞는지 본다.
- 결과는 accuracy 하나로 끝내지 말고 confusion matrix, precision/recall, calibration, overfitting을 함께 해석한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 모델이 복잡하면 항상 좋은 것이 아니라 데이터 수, noise, regularization과 함께 봐야 한다.
- test set으로 hyperparameter를 고르면 일반화 성능을 과대평가한다.
- loss를 최소화하는 것과 실제 의사결정 비용을 최소화하는 것은 다를 수 있다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 주제의 가정이 깨지는 데이터는 어떤 모습인가?
- regularization이나 prior가 모델의 해를 어느 방향으로 움직이는가?
- 성능이 나빠졌을 때 데이터, feature, loss, optimization 중 무엇부터 의심할 것인가?
- **01. 머신러닝 개요**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

다음: [02. 점추정과 Maximum Likelihood Estimation](02-point-estimation-mle.md)
