---
layout: page
title: "01. 머신러닝 개요"
permalink: /studies/ai/machine-learning/01-introduction/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `01` 정리·보강
- 표준 ML 교재(ISL · UW-ML) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

머신러닝은 **데이터(경험)로부터 성능이 개선되는 알고리즘**을 연구한다. 규칙을 사람이 모두 프로그래밍하는 대신, 데이터에서 함수·분류기·검색 모델·군집 구조를 학습한다.

$$
\text{Data} \;\longrightarrow\; \text{Learning algorithm} \;\longrightarrow\; \text{Model } f_\theta
$$

## 1. 역사적 흐름

| 시기 | 사건 | 의미 |
|---|---|---|
| 1936 | Turing machine | 계산 가능성·알고리즘의 이론적 기반 |
| 1950 | Turing test | 기계 지능 판별 사고실험 |
| 1956 | Dartmouth conference | "Artificial Intelligence" 분야의 출발 |
| 1957 | Perceptron | 초기 신경망 |
| 1969–1985 | AI winter | perceptron 한계, 기대 하락 |
| 1986~ | MLP + backpropagation | 다층 신경망 학습 재부상 |
| 2006~ | Deep learning | 깊은 신경망 + 대규모 데이터 |
| 2012~ | AlexNet / ImageNet | 현대 deep learning 폭발의 계기 |

## 2. 머신러닝 파이프라인

1. 데이터 수집
2. 입력 $x$와 출력 $y$ 정의
3. 모델 구조(가설 공간) 선택
4. 학습 알고리즘으로 파라미터 $\theta$ 추정
5. 새 데이터에 예측/결정
6. 성능 평가 → 개선

## 3. 대표 문제 유형

| 유형 | 출력 | 지도 여부 | 예 |
|---|---|---|---|
| **Regression** | 연속값 $y\in\mathbb{R}$ | 지도 | 집값·주가·retweet 수 예측 |
| **Classification** | 범주형 label | 지도 | spam 분류, 이미지 인식, 의료 진단 |
| **Retrieval** | query와 유사한 item | (지도/비지도) | 검색엔진, 유사 이미지, 추천 |
| **Clustering** | label 없는 그룹 | 비지도 | 문서 주제화, 사용자 세분화 |
| **Embedding / 표현학습** | similarity 보존 vector | (자기지도 등) | 의미적으로 가까운 것이 공간적으로 가깝게 |

- binary vs multiclass classification: 출력 범주가 2개 vs 3개 이상.
- **embedding**: 복잡한 데이터를 유사도가 보존되는 vector space로 사상 → 가까우면 의미도 비슷.

## 4. Deep Learning의 확산 (2012~)

세 요소의 합작: **대규모 데이터 + GPU 연산 + 알고리즘 개선(ReLU·BN·dropout·better init/optimizer·augmentation)**. classification을 넘어 detection·segmentation·pose·video·RL·생성까지 확장.

## 복습 질문

- AI와 ML의 관계, 그리고 "규칙 프로그래밍" vs "데이터에서 학습"의 차이는?
- Regression / classification / retrieval / clustering을 출력 형식과 지도 여부로 구분하라.
- deep learning 도약의 3요소와, 각각이 왜 필요한지 설명하라.
{% endraw %}

---

다음: [02. 점추정과 MLE](02-point-estimation-mle.md)
