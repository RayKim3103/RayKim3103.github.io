---
layout: page
title: "07. 앙상블 학습 (Bagging · Random Forest · Boosting)"
permalink: /studies/ai/machine-learning/07-ensemble-learning/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `12` 정리·보강

{% raw %}
## 개요

여러 약한 분류기를 결합해 강한 분류기를 만든다. **Bagging**은 bootstrap sample로 모델을 병렬 학습해 variance↓. **Random Forest**는 tree bagging + split마다 feature randomness. **Boosting**은 이전 모델이 틀린 sample에 집중하며 순차 학습(bias↓).

## 1. 왜 결합하면 좋아지나 — variance 관점

$$B$$개 예측기 각각 분산 $$\sigma^2$$, 쌍상관 $$\rho$$일 때 **평균 예측**의 분산:
$$
\mathrm{Var}\!\left(\frac{1}{B}\sum_b f_b\right) = \rho\sigma^2 + \frac{1-\rho}{B}\sigma^2
$$
- $$B\to\infty$$: 두 번째 항 소멸 → 분산 $$\to \rho\sigma^2$$.
- 따라서 **예측기 간 상관 $$\rho$$를 낮추는 것**이 핵심. bagging(다른 데이터) + random forest(다른 feature)가 $$\rho$$를 떨어뜨린다.
- bias는 거의 그대로 → **bias가 낮고 variance가 높은 base learner**(깊은 tree)와 궁합.

## 2. Bagging (Bootstrap Aggregation)

1. training set(크기 $$N$$)에서 **복원추출**로 같은 크기 bootstrap dataset $$B$$개 생성
2. 각 dataset에 모델 학습
3. regression → 평균, classification → majority vote (또는 확률 평균)

- 한 sample이 특정 bootstrap에 **안 뽑힐 확률** $$\approx (1 - 1/N)^N \to e^{-1} \approx 0.368$$ → 각 tree는 약 63.2%의 고유 sample로 학습.
- 나머지 **OOB(out-of-bag) sample**로 각 tree를 검증 → 별도 validation set 없이 일반화 오차 추정.
- **decision tree처럼 데이터 변화에 민감한(high-variance) 모델에 특히 효과적.** 안정적 모델(선형회귀 등)엔 이득 작음.

## 3. Random Forest

decision tree bagging + **각 split에서 전체 $$D$$개 feature가 아니라 무작위 $$m$$개 subset만** 고려 (분류 $$m \approx \sqrt{D}$$, 회귀 $$m \approx D/3$$).

효과:
- 한 강한 feature가 모든 tree의 최상단을 지배하는 것을 막음 → tree 간 **correlation $$\rho$$ 감소** → §1의 분산 감소 효과 증대
- tree는 보통 **가지치기 없이 최대로** 성장(bias 낮게 유지)
- feature importance: split에서의 불순도 감소 합, 또는 OOB permutation importance

## 4. Boosting

learner를 **순차**로 학습. 각 단계에서 이전 learner들이 틀린 sample의 weight를 키워 다음 learner가 어려운 sample에 집중.

### AdaBoost (분류, $$y \in \{-1, +1\}$$)

1. $$w_i^{(1)} = 1/N$$
2. $$t = 1, \dots, T$$:
   - 현재 weight로 weak classifier $$f_t$$ 학습, weighted error $$\epsilon_t = \sum_i w_i^{(t)}\mathbb{1}[f_t(x_i)\ne y_i]$$
   - 계수 $$\displaystyle \alpha_t = \frac{1}{2}\ln\frac{1 - \epsilon_t}{\epsilon_t}$$  (에러 낮을수록 큼; $$\epsilon_t = 0.5$$면 $$\alpha_t = 0$$)
   - weight 갱신 $$w_i^{(t+1)} \propto w_i^{(t)}\exp(-\alpha_t y_i f_t(x_i))$$ → 틀린 sample($$y_i f_t < 0$$) weight↑, 맞춘 sample weight↓, 그 후 normalize
3. $$\displaystyle F(x) = \sum_t \alpha_t f_t(x),\quad \hat y = \operatorname{sign}(F(x))$$

**이론**: 각 $$\epsilon_t = 0.5 - \gamma_t$$ ($$\gamma_t > 0$$, random보다 조금 나음)이면 training error $$\le \prod_t 2\sqrt{\epsilon_t(1-\epsilon_t)} = \prod_t\sqrt{1 - 4\gamma_t^2} \le \exp(-2\sum_t \gamma_t^2)$$ → **지수적으로 감소**.

**해석**: AdaBoost = **exponential loss** $$\sum_i \exp(-y_i F(x_i))$$의 **forward stagewise additive** 최소화. (Gradient Boosting은 이를 임의의 미분가능 loss로 일반화 — residual/음의 gradient에 tree를 순차 적합.)

- bias·variance 모두 줄일 수 있으나, $$T$$가 너무 크면 noise·outlier에 overfit → **$$T$$는 validation/CV로**.
- base learner는 보통 **얕은 tree**(stump ~ depth 3): 약하지만 random보다 나은 정도.

## 5. Bagging vs Boosting

| | Bagging / Random Forest | Boosting (AdaBoost / GBM) |
|---|---|---|
| 학습 방식 | 병렬, 독립 | 순차 (이전 결과에 의존) |
| 데이터 | bootstrap sample | 전체, 틀린 sample에 큰 weight |
| base learner | 깊은 tree (low bias, high var) | 얕은 tree (high bias, low var) |
| 주 효과 | **variance↓** (decorrelation) | **bias↓** + 어려운 sample 집중 |
| 병렬화 | 쉬움 | 어려움 (stage 의존) |
| 과적합 | $$B$$↑ 해도 안전 | $$T$$↑ 하면 위험 → 조기 종료 |
| 대표 | Random Forest | XGBoost / LightGBM |

## 복습 질문

- 평균 예측의 분산 $$\rho\sigma^2 + \frac{1-\rho}{B}\sigma^2$$을 쓰고, 앙상블 개선의 핵심이 왜 "$$\rho$$ 낮추기"인지 설명하라.
- bootstrap에서 한 sample이 빠질 확률 $$\approx e^{-1}$$의 유도와, OOB error의 쓰임새는?
- Random Forest가 split마다 feature subset을 쓰는 이유를 §1과 연결해 설명하라.
- AdaBoost의 $$\alpha_t = \frac12\ln\frac{1-\epsilon_t}{\epsilon_t}$$, weight 갱신식의 직관, training error가 $$\exp(-2\sum\gamma_t^2)$$로 감소하는 의미는?
- AdaBoost가 어떤 loss의 stagewise 최소화인가? bagging은 $$B$$를 키워도 안전한데 boosting은 $$T$$를 조심해야 하는 이유는?
{% endraw %}

---

이전: [06. 결정 트리](06-decision-tree.md) · 다음: [08. 분류기 평가 (Precision·Recall)](08-evaluating-classifiers.md)
