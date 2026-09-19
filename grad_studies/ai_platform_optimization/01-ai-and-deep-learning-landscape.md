---
layout: page
title: "01. AI와 딥러닝 시스템의 지형도"
permalink: /grad_studies/ai_platform_optimization/01-ai-and-deep-learning-landscape/
sitemap: false
---

- **강의**: 1. Deep Learning Overview (1)
- **읽을거리**: Pedro Domingos, *A Few Useful Things to Know About Machine Learning*, CACM 2012

{% raw %}
## 개요

이 장은 본격적인 시스템 최적화에 들어가기 전, (1) AI/딥러닝이 왜 지금 이 시점에 산업의 중심이 되었는지를 역사적으로 훑고, (2) 머신러닝의 기본 어휘(지도/비지도/강화학습, bias-variance, overfitting)를 정리하고, (3) 머신러닝 시스템을 구성하는 하드웨어 지형(GPU/TPU/NPU)을 개관한다.

## AI의 세 번의 물결 (AI Waves)

| 시기 | 국면 | 핵심 사건 |
|---|---|---|
| 1950–1974 | AI의 탄생 | 1950 튜링 테스트, 1951 최초의 신경망 기계 SNARC(Minsky), DARPA의 대규모 투자, 낙관론 |
| 1974–1980 | 1차 AI 겨울 | 컴퓨팅 파워·데이터 부족으로 기대에 못 미침 → 정부 지원 중단 |
| 1980–1987 | 2차 AI 물결 | Expert System 실용화 (CMU의 XCON, DEC에서 연 4천만 달러 절감), 지식(규칙) + 로직 결합 |
| 1987–1993 | 2차 AI 겨울 | 전용 AI 하드웨어 시장 붕괴 — 범용 하드웨어가 더 빠르게 발전하면서 AI 전용 칩 회사 300여 개가 시장에서 사라짐. 정부 지원 재차 중단 |
| 1993–2011 | 과도기 | 범용 하드웨어가 충분히 빨라짐 → 1997 Deep Blue가 가리 카스파로프를 이김, 음성 인식·검색 엔진 발전. 최적화·통계·확률·정보이론 등 이론적 토대 축적 |
| 2011–현재 | 3차 물결 (현재진행형) | 대규모 데이터 + HW/SW 발전이 복잡한 모델의 설계·학습을 가능케 함. 자율주행·추천 시스템 등 신규 응용 폭발 |

**교훈**: 두 번의 AI 겨울 모두 "이론은 있었지만 이를 뒷받침할 데이터와 컴퓨팅 파워가 없어서" 왔다. 즉 AI 혁명의 원동력은 항상 **알고리즘 자체보다 시스템(데이터+연산력)**이었다 — 이것이 이 강의 전체를 관통하는 전제다.

### AI 혁명의 다섯 가지 원천

```text
Data + Computation Power + Abstractions + 알고리즘 발전 + 모델 발전
```

여기서 "Abstractions"(추상화)는 PyTorch/TensorFlow 같은 프레임워크가 제공하는 프로그래밍 편의성을 뜻한다 — 연구자가 하드웨어를 몰라도 아이디어를 빠르게 구현할 수 있게 해주는 계층이며, 이 강의 Part 2(Ch.4~5)의 주제이기도 하다.

### AI ↔ 시스템의 양방향 시너지

- **AI가 시스템 문제를 푼다**: 강화학습, RNN, Attention, BERT 등은 원래 시스템/자원 관리 문제(스케줄링, 캐시 정책 등)에도 응용된다.
- **시스템이 AI를 발전시킨다**: Dynamic Neural Nets, 분산 학습(Distributed Training), 분산 AutoML 등은 모두 시스템 발전이 있었기에 가능했다.

## 머신러닝 101

> "A computer program is said to learn from experience (E) with respect to some task (T) and some performance measure (P), if its performance on T, as measured by P, improves with experience E." — Tom Mitchell, 1998

이미지 분류를 예로 들면:

- **Task (T)**: 이미지 클래스 예측
- **Experience (E)**: 레이블이 붙은 이미지 관측 데이터 (학습 데이터)
- **Performance (P)**: 정답률

ML 알고리즘을 구성하는 세 요소는 각각 **데이터(training set)**, **비용/손실 함수(cost/loss function, 예: MSE)**, **최적화 전략을 가진 모델(예: 회귀 모델 + gradient descent)**로 대응된다.

### 지도/비지도/강화학습

| 구분 | 데이터 | 대표 과제 |
|---|---|---|
| 지도학습 (Supervised) | 레이블 있음 | 회귀(연속값), 분류(범주값) |
| 비지도학습 (Unsupervised) | 레이블 없음 | 군집화(clustering), 차원 축소 |
| 강화학습 (Reinforcement) | 보상(reward) | 정책 최적화 |

지도학습의 흐름은 "데이터(입력+정답) → 모델(함수를 충분히 표현력 있게 구성) → 학습(최적 파라미터 탐색) → 새 데이터에 대한 정답 예측"이다. 입력 데이터의 구조에 따라 적합한 아키텍처도 달라진다 — 공간 정보는 CNN(Convolutional Network), 순차 정보는 RNN(Recurrent Network), 그래프 정보는 GNN(Graph Network).

### Bias-Variance Trade-off와 Overfitting

- **Bias가 낮은 모델**은 보통 **variance가 높다** (반대도 성립). Variance가 높으면 더 많은 데이터로 보완할 수 있다.
- 딥러닝에서는 모델 구조 자체보다 **학습 기법**(초기화, SGD, Dropout, learning rate)이 bias-variance 균형에 더 큰 영향을 준다.
- **Overfitting**: 학습 알고리즘이 최소화하려는 목적(학습 손실)과 우리가 실제로 최소화하고 싶은 목적(일반화 오차)이 다르기 때문에 발생한다. 학습 손실은 계속 낮아지지만 검증/테스트 손실은 어느 시점부터 다시 올라간다.

### 데이터셋 크기의 힘

> "단순한 알고리즘 + 많은 데이터"가 "정교한 알고리즘 + 적은 데이터"를 이기는 경우가 많다.

다만 항상 그런 것은 아니며, 데이터가 많아질수록 오히려 더 정교한 모델링이 필요해지는 경우도 있다 (예: 노이즈·바이어스가 섞인 대규모 데이터).

### Domingos(2012)의 12가지 교훈 (1주차 권장 독서)

1. Learning = Representation + Evaluation + Optimization
2. It's generalization that counts
3. Data alone is not enough
4. Overfitting has many faces
5. Intuition fails in high dimensions
6. Theoretical guarantees are not what they seem
7. Feature engineering is the key
8. More data beats a cleverer algorithm
9. Learn many models, not just one
10. Simplicity does not imply accuracy
11. Representable does not imply learnable
12. Correlation does not imply causation

이 논문은 2012년 작성됐지만, 시스템 최적화 관점에서도 여전히 유효하다 — 특히 "8. 정교한 알고리즘보다 많은 데이터"와 "1. 표현+평가+최적화"는 이후 강의에서 다룰 "모델 구조를 바꾸기보다 시스템을 최적화하는 것이 더 실용적인 경우가 많다"는 메시지와 맞닿아 있다.

## 머신러닝 라이프사이클과 시스템 경계

```text
Model Development → Training → Inference
```

| 단계 | 하는 일 |
|---|---|
| Model Development | 데이터 수집·정제, feature engineering, 모델 설계 |
| Training | 대규모 데이터로 학습, 재학습(retraining), 정확도 검증, 모델 버저닝 |
| Inference | 대량 요청 하에서 빠른 예측. 주기적(batch) 또는 온라인(continuous) 방식으로 모델을 갱신 |

이 강의(그리고 이후 chapter 전체)는 주로 **Training의 효율화**와 **Inference의 지연시간/처리량 최적화**를 다룬다. Model Development(어떤 모델 구조를 쓸지)는 "Deep Learning 이론" 영역이라 상대적으로 가볍게 다룬다.

## 머신러닝을 구동하는 하드웨어 지형

| 하드웨어 | 특징 | 전력 효율 |
|---|---|---|
| **GPU (NVIDIA Volta/Ampere/Hopper/Blackwell)** | 범용 병렬 컴퓨팅, CUDA 생태계가 압도적 | ~0.05 TFLOPS/W |
| **TPU (Google)** | TensorFlow 전용 ASIC, 고정 함수 + 정수 연산 | ~2.3 TOPS/W |
| **FPGA 기반 (Azure Brainwave/Catapult)** | 재구성 가능한 클러스터 | ~0.3 TFLOPS/W |
| **Nervana (Intel, 단종)** | Flexpoint 연산 | - |
| **Cambricon (DianNao 계열)** | 화웨이 Kirin에 탑재, 고정소수점 연산 | ~1 TFLOPS/W |
| **EIE/ESE (Aristotle/Descartes)** | FPGA 구현, SW/HW Co-design | - |

### NVIDIA GPU 세대별 흐름

- **Volta V100**: 210억 트랜지스터, FP32 15.7 TFLOPS, Tensor Core 기반 혼합정밀 125 TFLOPS, TDP 300W
- **DGX-2**: V100 16장 + 30TB NVMe + 12x NVSwitch
- **A100 → H100 → B200**: CUDA 코어 수 증가, 메모리 용량 증가, 그리고 무엇보다 **새로운 아키텍처 기능**이 중요하다 — FP64/FP32/FP16/BF16/FP8/MX 포맷 지원 확대, TMA(Tensor Memory Accelerator) 등. 이런 저정밀 포맷·전용 유닛의 발전이 Ch.3(양자화)·Ch.8(NPU)의 배경이 된다.

### Google TPU / Tesla FSD

- **TPU**: TensorFlow 전용 ASIC, 클라우드용(대형)과 Edge TPU(Coral Dev Board, 4 TOPS, 2 TOPS/W, TensorFlow Lite 지원)로 나뉜다. TPU 아키텍처는 Ch.8에서 systolic array 구조와 함께 자세히 다룬다.
- **Tesla FSD 칩**: 자율주행용 SoC로 CPU+GPU+커스텀 NPU를 한 칩에 통합 — 이 역시 Ch.8에서 심화한다.

## 왜 이 순서로 강의가 구성되었는가

정리하면, "데이터+연산력이 AI를 이끈다"는 역사적 교훈으로부터 **"모델 이론보다 이를 뒷받침하는 시스템(프레임워크·컴파일러·하드웨어)이 실질적인 성능을 좌우한다"**는 강의 전체의 문제의식이 도출된다. 다음 장(Ch.2)부터는 실제로 DNN의 연산이 하드웨어에서 어떻게 계산되는지(Convolution/FC/Pooling)를 들여다보며 시스템 최적화의 대상을 구체화한다.
{% endraw %}

---

이전: [00. 강의 개요](00-course-overview.md) · 다음: [02. CNN 아키텍처와 DNN 핵심 연산](02-cnn-architectures-and-dnn-core-operations.md)
