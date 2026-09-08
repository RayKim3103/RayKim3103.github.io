---
layout: page
title: "07. 앙상블 학습 (Bagging · Random Forest · Boosting)"
permalink: /studies/ai/machine-learning/07-ensemble-learning/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `12` 정리·보강
- 표준 ML 교재(ISL·ESL) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

여러 약한 분류기를 결합해 강한 분류기를 만든다. **Bagging**은 bootstrap sample로 모델을 병렬 학습해 variance↓. **Random Forest**는 tree bagging + split마다 feature randomness. **Boosting**은 이전 모델이 틀린 sample에 집중하며 순차 학습(bias↓).

## 1. 아이디어

여러 learner가 **서로 다른 오류**를 만들면, 평균/투표로 더 안정적인 예측을 얻는다.

## 2. Bagging (Bootstrap Aggregation)

1. training set에서 **복원추출**로 같은 크기의 bootstrap dataset을 $B$개 생성
2. 각 dataset에 모델 학습
3. regression → 평균, classification → majority vote

- bootstrap: 어떤 sample은 여러 번, 어떤 sample은 빠짐 → learner마다 다른 데이터 → **diversity**.
- **variance 감소**에 특히 효과적 → decision tree처럼 데이터 변화에 민감한 모델과 궁합이 좋다.
- (부수 효과) 각 tree가 안 쓴 sample로 **OOB(out-of-bag) error** 추정 가능.

## 3. Random Forest

decision tree bagging + **각 split에서 전체 feature가 아니라 무작위 feature subset만** 고려.

효과:
- tree 사이 **correlation 감소** → 평균/투표의 variance 감소 효과 증대
- 많은 feature가 있는 문제에서 강력

## 4. Boosting

learner를 **순차**로 학습. 각 단계에서 이전 learner들이 틀린 sample의 weight를 키워 다음 learner가 어려운 sample에 집중.
$$
\text{맞춘 sample} \to \text{weight}\downarrow,\qquad \text{틀린 sample} \to \text{weight}\uparrow
$$
bias·variance 모두 줄일 수 있으나, 너무 오래 반복하면 overfitting.

### AdaBoost
1. 모든 sample weight를 균등하게
2. 현재 weight로 weak classifier 학습
3. weighted error 계산
4. classifier 계수 $\alpha_t$ 계산 (weighted error 낮을수록 큼)
5. 틀린 sample weight↑, 맞춘 sample weight↓
6. weight normalize
7. $T$회 반복

$$
\text{score}(x) = \sum_t \alpha_t f_t(x),\qquad \hat y = \operatorname{sign}(\text{score}(x))
$$
이론: 각 weak classifier가 random보다 조금만 나아도 training error가 빠르게(경우에 따라 0까지) 감소. 반복 수 $T$는 validation/CV로 선택.

## 5. Bagging vs Boosting

| | Bagging | Boosting |
|---|---|---|
| 학습 방식 | 병렬, 독립 | 순차 |
| 데이터 weight | bootstrap sample | 틀린 sample에 큰 weight |
| 주 효과 | **variance↓** | **bias↓** + 어려운 sample 집중 |
| 대표 모델 | Random Forest | AdaBoost / Gradient Boosting |

## 복습 질문

- weak learner를 앙상블로 결합하면 왜 좋아지는가?
- bagging과 bootstrap sample의 관계, 그리고 왜 variance를 줄이는가?
- Random Forest가 split마다 feature subset을 쓰는 이유는?
- AdaBoost의 sample weight·계수 $\alpha_t$ 갱신 직관과, bagging vs boosting의 차이는?
{% endraw %}

---

이전: [06. 결정 트리](06-decision-tree.md) · 다음: [08. 분류기 평가 (Precision·Recall)](08-evaluating-classifiers.md)
