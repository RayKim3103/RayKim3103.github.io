---
layout: page
title: "12. Project — CARE (Long-tailed Recognition)"
permalink: /studies/ai/deep-learning-experiment/12-project-care-long-tailed-recognition/
sitemap: false
---

- **원본**: [GitHub — Deep Learning Experiment](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Deep_Learning_Experiment) · 사전보고서(25) + 본보고서(13) 통합·보강
- 이론 배경은 long-tailed recognition 문헌(cRT, Balanced Softmax, LDAM 등) 수준으로 보강. **실험 설정·결과 수치는 원 보고서 그대로**입니다.

{% raw %}
## 개요

CIFAR-100 **long-tailed split**에서 head class bias를 줄이기 위해 **CARE(Class-Aware Rebalancing Experts)**를 구현. Stage 1에서 강한 표현을 학습하고, Stage 2에서 목적이 다른 **expert classifier들을 결합**해 tail 성능을 보완.

## 1. 이론 배경 — long-tailed recognition

long-tailed dataset에서는 소수 head class에 sample이 집중되고, tail class는 **representation과 classifier boundary가 모두 불안정**하다. 단순 CE는 head class prior를 강하게 반영해 tail decision boundary가 흔들린다.

| 기법 | 아이디어 |
|---|---|
| **class-balanced loss** | *effective number of samples*로 rare class에 더 큰 가중 |
| **cRT** (classifier re-training) | representation은 전체 데이터로 학습 → **classifier만** class-balanced sampling으로 재학습 |
| **Balanced Softmax** | training class prior를 softmax denominator에 반영해 **prior shift 보정** |
| **LDAM** | class별 **margin**을 다르게 → minority class에 더 큰 decision margin |
| **logit adjustment** | class prior의 log를 logit에 ± → prior bias 보정 |
| **multi-expert** | 서로 다른 objective의 classifier 결합 → head/medium/tail trade-off 완화 |

$$
\text{long-tail split: } n_c = 450 - 4c \quad(\text{class 0: 450장, class 99: 54장})
$$

CARE의 핵심: **representation adaptation**과 **classifier rebalancing**을 분리 + 한 objective가 head·tail을 동시에 최적화하기 어렵다는 관찰에서 multi-expert.

## 2. 실습 설계

- CIFAR-100 train에서 class당 **50장을 validation**으로 분리, 나머지에 $n_c=450-4c$ 규칙으로 long-tail split.
- **ResNet18CIFAR**: 첫 conv를 3×3 stride 1로 바꾸고 maxpool 제거. classifier는 **cosine classifier**(feature·weight normalize → logit을 angular similarity로, head bias 완화).
- **Stage 1**: label smoothing + MixUp + CutMix + RandomErasing + cosine schedule → regularized representation.
- **Stage 2**: early visual layer freeze, layer3/layer4 + expert head fine-tuning.
  - Expert 0: cRT / class-balanced
  - Expert 1: Balanced Softmax
  - Expert 2: LDAM + **class-center alignment** (Stage 1 backbone feature의 class-wise 평균으로 center 계산, tail-aware center init)
- **validation macro accuracy**로 tau / aggregation rule / teacher mix / horizontal flip TTA 선택 → test 보고.

## 3. 결과

| | overall | head | medium | tail | macro |
|---|---|---|---|---|---|
| Stage 1 (val) | — | — | — | — | **73.5 %** |
| Stage 1 (test) | — | — | — | — | 73.35 % |
| cRT baseline (test) | 73.49 % | 80.56 % | 74.52 % | 65.18 % | — |
| **CARE 최종 (test)** | **76.30 %** | 79.85 % | 76.64 % | **72.30 %** | **76.30 %** |

- CARE 최종 **validation macro 77.02 %** (proposed 후보 중 최고).
- best 설정: **tau = 1.25**, aggregation = `prior_g0.50`, teacher mix = 0.7, TTA = horizontal flip.
- cRT 대비 **tail 65.18 % → 72.30 %** 로 크게 개선, head는 소폭 하락(80.56 → 79.85).

## 4. 자주 틀리는 지점

- validation set은 **balanced** → macro accuracy 기준 model selection에 적합하지만 test distribution 해석과 분리해야.
- **teacher mix**는 head/medium 성능 저하를 막지만 tail 보정 효과를 희석 → validation으로 조절.
- **expert aggregation**은 단순 평균 / prior-aware weighting / temperature(tau) calibration에 따라 결과가 크게 달라짐.

## 복습 질문

- long-tailed recognition이 "sample 수가 적은 문제"만이 아닌 이유(representation·prior·calibration)는?
- cRT가 효과적인 이유(무엇을 전체 데이터로, 무엇을 balanced로 학습하는가)는?
- Balanced Softmax와 LDAM이 각각 prior/margin을 어떻게 다루는가?
- CARE의 multi-expert 설계 동기와, cosine classifier가 head bias를 줄이는 방식은?
{% endraw %}

---

이전: [11. Lab13 — RNN·Seq2Seq·Attention](11-lab13-rnn-seq2seq-attention.md)
