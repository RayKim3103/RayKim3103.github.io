---
layout: page
title: "01. 머신러닝 개요"
permalink: /studies/ai/machine-learning/01-introduction/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `01` 정리·보강

{% raw %}
## 개요

머신러닝은 **데이터(경험)로부터 성능이 개선되는 알고리즘**을 연구한다. 규칙을 사람이 모두 프로그래밍하는 대신, 데이터에서 함수·분류기·검색 모델·군집 구조를 학습한다.

$$
\text{Data} \;\longrightarrow\; \text{Learning algorithm} \;\longrightarrow\; \text{Model } f_\theta
$$

- **Tom Mitchell의 정의**: 어떤 작업 $$T$$, 성능 척도 $$P$$, 경험 $$E$$에 대해, $$E$$가 쌓일수록 $$T$$에 대한 $$P$$가 개선되면 그 프로그램은 학습한다고 말한다.
- 전통 프로그래밍은 `규칙 + 입력 → 출력`, 머신러닝은 `입력 + 출력 예시 → 규칙(모델)`.

## 1. 역사적 흐름

| 시기 | 사건 | 의미 |
|---|---|---|
| 1936 | Turing machine | 계산 가능성·알고리즘의 이론적 기반 |
| 1950 | Turing test | 기계 지능 판별 사고실험 |
| 1956 | Dartmouth conference | "Artificial Intelligence" 분야의 출발 |
| 1957 | Perceptron (Rosenblatt) | 초기 신경망, 선형 분류기 |
| 1969 | Minsky & Papert, *Perceptrons* | 단층 perceptron이 XOR 불가 지적 |
| 1969–1985 | AI winter | 기대 하락, 자금 축소 |
| 1986~ | MLP + backpropagation (Rumelhart 등) | 다층 신경망 학습 재부상 |
| 1990s~ | SVM, ensemble, graphical model | 통계적 학습 이론의 시대 |
| 2006~ | Deep learning (Hinton 등) | 깊은 신경망 사전학습·대규모 데이터 |
| 2012 | AlexNet / ImageNet | top-5 error 26% → 16%, 현대 deep learning 폭발의 계기 |

## 2. 머신러닝 파이프라인

1. 데이터 수집
2. 입력 $$x$$와 출력 $$y$$ 정의 (feature engineering / representation)
3. 모델 구조(**가설 공간** $$\mathcal{H}$$) 선택
4. **목적 함수**(loss + 정규화) 정의, 학습 알고리즘으로 파라미터 $$\theta$$ 추정
5. 새 데이터에 예측/결정
6. **held-out 데이터로 평가** → 모델·하이퍼파라미터 개선

핵심 구분: **모델**(가설 공간) / **목적 함수**(무엇을 최소화하나) / **최적화 알고리즘**(어떻게 최소화하나). 이 세 축으로 거의 모든 지도학습을 정리할 수 있다.

## 3. 대표 문제 유형

| 유형 | 출력 | 지도 여부 | 예 |
|---|---|---|---|
| **Regression** | 연속값 $$y\in\mathbb{R}$$ | 지도 | 집값·주가·retweet 수, 뇌 intensity → 인지 상태 |
| **Classification** | 범주형 label | 지도 | spam 분류, 이미지 인식, 의료 진단, 감성 분석 |
| **Retrieval** | query와 유사한 item | (지도/비지도) | 검색엔진, 유사 이미지, 뉴스 추천 |
| **Clustering** | label 없는 그룹 | 비지도 | 문서 주제화, 사용자 세분화, 이미지 결과 정리 |
| **Embedding / 표현학습** | similarity 보존 vector | (자기지도 등) | 의미적으로 가까운 것이 공간적으로 가깝게 |

- **binary vs multiclass**: 출력 범주가 2개 vs 3개 이상. (multi-label은 한 sample에 여러 label 동시.)
- **embedding**: 복잡한 데이터를 유사도가 보존되는 vector space로 사상 → 가까우면 의미도 비슷 → retrieval·clustering·downstream 학습에 재사용.
- 그 외: **ranking**(검색 결과 순서), **structured prediction**(출력이 sequence·tree·graph), **reinforcement learning**(보상으로 정책 학습), **density estimation / generation**.

## 4. Deep Learning의 확산 (2012~)

세 요소의 합작:

| 요소 | 왜 필요한가 |
|---|---|
| **대규모 데이터** (ImageNet 등) | 파라미터가 많은 모델이 overfit하지 않으려면 충분한 sample 필요 |
| **GPU 병렬 연산** | 행렬곱 중심 연산을 수십~수백 배 가속 → 큰 모델을 현실적 시간에 학습 |
| **알고리즘 개선** | ReLU(gradient 소실 완화), BatchNorm, dropout, 더 나은 init/optimizer(Adam), data augmentation |

classification을 넘어 detection·segmentation·pose·video·RL·text-to-image 생성까지 확장.

## 복습 질문

- Mitchell의 $$T/P/E$$ 정의로 "spam 필터가 학습한다"를 서술하라. 전통 프로그래밍과의 차이는?
- 지도학습을 **모델 / 목적 함수 / 최적화**의 세 축으로 나누는 것이 왜 유용한가?
- Regression / classification / retrieval / clustering / embedding을 출력 형식과 지도 여부로 구분하라.
- deep learning 도약의 3요소와, 각각이 왜 필요한지 설명하라. ImageNet·AlexNet(2012)의 의미는?
{% endraw %}

---

다음: [02. 점추정과 MLE](02-point-estimation-mle.md)
