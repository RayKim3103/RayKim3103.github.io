---
layout: page
title: "06. 메모리 최적화와 네트워크 프루닝"
permalink: /grad_studies/ai_platform_optimization/06-memory-optimization-and-network-pruning/
sitemap: false
---

- **강의**: 6. Optimizations for Hardware Backends (Part 1: Memory / Pruning)

{% raw %}
## 개요

Ch.2~3에서 반복적으로 언급된 문제 — "네트워크가 깊어질수록, skip connection이 많을수록 중간 activation을 메모리에 계속 들고 있어야 한다" — 를 이 장에서 본격적으로 다룬다. 전반부는 **계산 그래프 기반 메모리 할당·재사용·재계산(rematerialization)** 기법을, 후반부는 **가중치 자체를 줄이는 네트워크 프루닝(pruning)**을 다룬다.

## 메모리 문제의 규모

- ResNet-1k(CIFAR-10), ResNet-200(ImageNet) 같은 최신 모델은 **자원 제약적(resource-bound)**이다.
- 긴 시퀀스를 처리하는 LSTM 같은 순환 모델도 마찬가지다.
- 모델의 최대 크기는 결국 **타깃 하드웨어의 총 RAM**(예: Titan X 12GB)으로 제한된다.

해법의 방향은 **그래프 레벨 메모리 최적화**, 구체적으로는 자동미분(Ch.3)이 만들어내는 계산 그래프를 활용하는 것이다.

## 계산 그래프 기반 Executor

예시: `exp(a*b + 3)`이라는 계산 그래프를 생각해보자.

```text
a, b → mul → add(+3) → exp
```

### Dynamic Memory Allocation (동적 메모리 할당)

가장 단순한 전략: **필요할 때 할당하고, 더 이상 필요 없으면 회수(recycle)한다.**

- `mul` 결과 메모리 할당 → `add`가 이를 소비하고 새 메모리에 결과 저장 → `mul`의 메모리는 더 이상 필요 없으면 회수 가능 → `exp`가 `add`의 결과를 소비

이 방식에서 메모리 크기는 대략 **연산 개수에 선형(linear)**으로 비례한다.

### Static Memory Allocation (정적 메모리 할당)

컴파일 시점에 미리 재사용 계획을 세우는 방식 — **컴파일러의 레지스터 할당(register allocation) 알고리즘과 유사**하다.

| 패턴 | 설명 | 제약 |
|---|---|---|
| **Inplace** | 연산 결과를 입력이 있던 자리에 그대로 저장 | 해당 입력을 소비하는 곳(consumer)이 **둘 이상**이면 사용 불가 (다른 소비자가 값을 못 읽게 됨) |
| **Normal Sharing** | 더 이상 필요 없는 메모리를 다른 노드가 재사용 | - |

> **Inplace가 안 되는 예시**: 같은 중간값이 `exp`와 `log` 양쪽에서 모두 소비된다면, 그 값을 다른 연산 결과로 덮어쓸 수 없다.

### Concurrency 고려사항

메모리 재사용 계획은 **동시 실행(parallel execution)** 가능성도 함께 고려해야 한다 — 두 연산이 병렬로 실행될 수 있다면, 서로의 메모리를 밟아서는 안 된다.

## 연산과 메모리의 트레이드오프: Rematerialization (Checkpointing)

**핵심 아이디어**: 메모리 제약이 심할 때는, forward pass의 **일부 중간 결과만 저장**하고, backward pass에서 그래디언트 계산에 필요한 값은 **다시 계산(recompute)**한다.

```text
Forward:   [저장 안 함, 재계산될 예정] ↔ [저장(checkpoint)]
Backward:  체크포인트로부터 국소적으로 재계산 후 그래디언트 계산
```

- forward path를 "거울처럼" 반영해 재계산이 일어나는 지점을 생성하고, 사용자는 **mirror factor**로 각 결과를 버릴지/유지할지 제어할 수 있다.
- 결과적으로 **Sublinear Memory Complexity**(메모리 사용량이 레이어 수보다 훨씬 느리게 증가)를 달성할 수 있다 — 대신 일부 연산을 중복 수행하는 성능 손실이 따른다.

> 참고 논문: *Training Deep Nets with Sublinear Memory Cost* (arXiv:1604.06174)

### 결론: 메모리 계획의 원칙

1. 계산 그래프는 의존성(dependency)을 고려하는 데 효과적이다.
2. 메모리 할당은 **동시성(concurrency)**과 **메모리 제약**을 함께 신중히 고려해야 한다.
3. **재계산(recompute) ↔ 메모리** 사이에는 트레이드오프가 있으며, (약간의 성능 손실을 감수하면) sublinear한 메모리 사용이 가능하다.

## 실무: Rematerialization의 현재적 의미 (강의 노트 기반)

- **Memoization**(모든 결과를 저장) vs **Rematerialization**(일부만 저장, 필요시 재계산)의 대비가 핵심이다.
- **LLM 시대의 핵심 응용**: forward+backward 양쪽에서 모든 activation을 저장하면 메모리가 폭발적으로 증가한다. Checkpointing으로 이 트레이드오프를 관리하는 것은 현재 대형 모델 **학습**의 표준 기법이며, 추론(inference) 단계에서도 GPU 메모리가 부족할 때 동일한 아이디어(재계산 vs 캐싱)가 KV Cache 관리 전략(Ch.9, Ch.11)에 그대로 이어진다.

## Neural Network Pruning

### 기본 개념

가중치 행렬에서 **절댓값이 threshold보다 작은 가중치를 제거**해 연산량과 저장 공간을 줄인다.

```text
|Weight| > Threshold  →  유지
|Weight| ≤ Threshold  →  제거(0으로)
```

> 참고: Han et al., *Learning both Weights and Connections for Efficient Neural Networks*, NIPS 2015

### 프루닝 효과를 어떻게 측정할 것인가

| 지표 종류 | 예시 | 특징 |
|---|---|---|
| **간접 지표(Indirect)** | 파라미터 개수, MAC 연산 수 | 하드웨어와 무관하게 측정 가능하지만 실제 속도를 보장하지 않음 |
| **직접 지표(Direct)** | Latency, Energy | 하드웨어에 의존적이지만 실제 이득을 정확히 반영 |

> **핵심 질문**: "파라미터 수를 줄이는 것이 옳은 지표인가?" — 답은 대체로 "아니오"에 가깝다. 실제 속도는 하드웨어가 sparse 연산을 얼마나 잘 활용하느냐에 달려있기 때문이다.

### Deep Compression (Han et al., ICLR 2016)

세 단계 파이프라인: **Pruning → Weight Sharing (양자화) → Huffman Coding**

1. **Pruning**: 연결(connection) 제거
2. **Weight Sharing**: 레이어별로 k-means 클러스터링을 적용해 **공유 가중치(shared weight)** 집합을 찾고, 이 클러스터 중심값들만 저장

   $$
   \text{minimize} \sum_{i} \|w_i - c_{k(i)}\|^2
   $$

   이후 공유 가중치로 fine-tuning을 수행해 정확도를 회복
3. **Huffman Coding**: 빈도가 낮은 값에 긴 코드를, 빈도가 높은 값에 짧은 코드를 배정해 추가 압축

### Energy-aware Pruning (CVPR 2017)

파라미터 수나 연산 수 대신, **에너지 소모 추정치를 직접 기준**으로 네트워크를 프루닝한다 — "직접 지표"를 프루닝 기준 자체로 사용하는 접근.

### Structured Sparsity (NIPS 2016)

**Unstructured sparsity(임의 위치의 개별 가중치 제거)는 실제 속도 향상으로 직결되지 않는 경우가 많다.** 하드웨어(특히 SIMD/GPU)가 임의 패턴의 sparse 연산을 효율적으로 처리하지 못하기 때문이다. 반면 **Structured Sparsity**(채널·필터·블록 단위로 통째로 제거)는 남은 부분이 여전히 dense한 작은 행렬이 되어 기존 dense 연산 커널을 그대로 쓸 수 있다.

### 하드웨어 인지형(HW-Aware) 프루닝: Scalpel (ISCA 2017)

**문제**: SIMD는 dense한 행렬-벡터 곱에는 강하지만, 임의 패턴의 sparse 행렬-벡터 곱(SpMV)에는 비효율적이다. SIMD 폭이 2라면 한 번에 2개 원소를 load/MAC해야 하는데, sparse 패턴이 SIMD 레인 경계와 맞지 않으면 일부 레인이 낭비된다.

**해법 — SIMD-aware Weight Pruning**:

1. 원본 가중치 행렬에서 **SIMD 폭 크기만큼 그룹**을 짓는다.
2. 그룹별 중요도를 RMS(root mean square)로 계산한다.
3. 중요도가 threshold보다 낮은 **그룹 전체**를 제거한다 (개별 원소가 아니라 그룹 단위!).

이렇게 하면 남은 가중치가 항상 SIMD 폭 단위로 정렬되어 있어, 컬럼 인덱스 저장 공간도 줄고 SIMD 유닛도 완전히 활용된다. ARM Cortex-M4 마이크로컨트롤러 실험에서, 동일 프루닝 비율 대비 **기존 방식보다 실행시간이 48~68% 단축**되었다.

### DeftNN: GPU에서의 Pruning 병목 (MICRO 2017)

**문제**: 단순히 가중치를 프루닝한다고 GPU에서 실제 성능이 좋아지지 않는다 — sparse 연산은 (1) 계산이 불규칙(irregular)하고 (2) on-chip 메모리 대역폭에 최적화되어 있지 않기 때문이다.

**해법**: **Synapse Vector Elimination**(기여도가 낮은 synapse 벡터 그룹을 GPU 아키텍처 친화적으로 탐색·제거 — reordering, matrix truncation, scale adjustment) + **Near-compute Data Fission**.

## 실무 종합: 프루닝은 언제 실제로 도움이 되는가 (강의 노트 기반)

> **실무 현실**: GPU/NPU는 **dense matrix 연산에 최적화**되어 있다. 프루닝 비율이 충분히 높지 않으면(예: unstructured sparsity가 90% 미만) 오히려 **latency가 증가**하는 경우가 많다.

| 기법 | 정확도 영향 | 메모리 절감 | Latency 영향 | GPU/NPU 적합도 | 권장 상황 |
|---|---|---|---|---|---|
| Unstructured Pruning | ↓↓ | ↑↑ | ↑ or ↓ | 낮음 | sparsity 90%↑일 때만 유효 |
| Structured Pruning | ↓ | ↑ | ↑ | 중간~높음 | **실무 표준** |

- 프루닝 후에는 정확도 손실을 회복하기 위한 **재학습(fine-tuning)**이 사실상 필수다.
- **Structured Pruning(채널/필터 단위)**이 unstructured 대비 하드웨어 친화적이라 실무에서 선호된다.
- 다음 장(Ch.7)에서는 이렇게 최적화된 모델을 CPU·엣지 디바이스에 실제로 배포할 때 생기는 추가적인 시스템 문제(클라우드-엣지 협업, CPU 추론 최적화)를 다룬다.

## 시험·복습 체크포인트

- Dynamic memory allocation과 Static memory allocation(inplace/sharing)의 차이는? Inplace 최적화가 불가능한 조건은?
- Rematerialization(checkpointing)이 무엇을 트레이드하는가? Sublinear memory complexity란?
- 프루닝 효과를 측정하는 직접 지표와 간접 지표의 차이는? 왜 직접 지표가 더 신뢰할 수 있는가?
- Deep Compression의 3단계(Pruning → Weight Sharing → Huffman Coding)를 설명할 수 있는가?
- Unstructured sparsity가 실제 속도 향상으로 잘 이어지지 않는 이유는? Structured sparsity는 이를 어떻게 해결하는가?
- Scalpel이 SIMD 효율을 높이기 위해 "그룹 단위" 프루닝을 쓰는 이유는?
{% endraw %}

---

이전: [05. 딥러닝 컴파일러](05-deep-learning-compilers.md) · 다음: [07. 엣지·CPU 추론 최적화](07-edge-and-cpu-inference-optimization.md)
