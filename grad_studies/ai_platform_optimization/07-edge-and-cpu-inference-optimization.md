---
layout: page
title: "07. 엣지·CPU 추론 최적화"
permalink: /grad_studies/ai_platform_optimization/07-edge-and-cpu-inference-optimization/
sitemap: false
---

- **강의**: 6. Optimizations for Hardware Backends (Part 2: Edge / CPU)

{% raw %}
## 개요

Ch.6이 "모델 자체를 어떻게 작게 만들 것인가(메모리·프루닝)"를 다뤘다면, 이 장은 "그렇게 만든 모델을 실제로 어디서, 어떻게 실행할 것인가"를 다룬다. 클라우드-엣지 협업 추론(collaborative inference)과, 여전히 광범위하게 쓰이는 CPU에서의 추론 최적화 두 축으로 구성된다.

## 왜 엣지(Edge) 추론이 필요한가

| 방식 | 장점 | 단점 |
|---|---|---|
| **Cloud-only 추론** | 높은 연산 성능 | 높은 데이터 통신 오버헤드(모바일 ↔ 클라우드) |
| **Mobile-only 추론** | 낮은 통신 오버헤드 | 낮은 연산 성능 |

이 둘의 트레이드오프를 조절하는 것이 엣지 최적화 연구의 핵심 문제다.

## Neurosurgeon: Collaborative Intelligence Between the Cloud and Mobile Edge (ASPLOS'17)

**문제**: Cloud-only도, Mobile-only도 각자 비효율적이다.

**해법**: DNN 연산을 **레이어 단위(layer granularity)**로 자동 분할해, 일부는 모바일에서, 일부는 클라우드(데이터센터)에서 실행한다. 어느 레이어를 경계로 나눌지는 네트워크 상태·디바이스 성능을 고려해 동적으로 결정된다.

## Adaptive Parallel Execution on Heterogeneous Edge Devices (SEC'19)

**문제**: 경량 엣지 디바이스 여러 대에 걸쳐 세밀한(fine-grained) 모델 병렬성을 적용하는 것은, **데이터 통신 오버헤드가 너무 커서** 비효율적이다.

**해법**:

- **Fused-layer level parallelization**: 레이어를 개별적으로가 아니라 묶어서(fused) 병렬화 — 중간 데이터 교환 횟수를 줄임
- 컴퓨팅 자원과 네트워크 상태를 함께 고려하는 **협업적 CNN 가속**
- 최적의 분할·병렬화를 찾기 위한 **동적 프로그래밍 기반 탐색**
- 성능 예측을 위한 **선형 회귀(linear regression) 모델**

> Layer partitioning은 "병렬화가 안 되는(non-parallel) 레이어를 가진 네트워크에서 어떻게 모델 병렬성을 활용할 것인가"라는 일반적인 질문에 대한 하나의 답이다.

## SIEVE: Speculative Inference on the Edge with Versatile Exportation (DAC'20)

**관찰**: Cloud-only와 Mobile-only 중 어느 쪽도 항상 우월하지 않다(No superior approach) — 상황(네트워크 상태, 입력 난이도)에 따라 유불리가 바뀐다.

**해법 — 추측적(speculative) 하이브리드 구조**:

```text
CNN Selector → (Speculative CNN 실행 시도) → Misspeculation Detector → (필요시 Original CNN으로 복구)
```

- 가볍고 빠른 **Speculative CNN**을 우선 실행하고, 오검출(misspeculation) 가능성을 탐지해 필요할 때만 원본(더 무거운) 모델로 전환/복구하는 **server-edge 하이브리드 시스템**.
- 실험 결과, 시나리오에 따라 Cloud-only 대비 최대 수 배(최대 약 1497% 등 극단적 케이스 포함)의 지연시간 변화가 관찰되며, **데이터 전송(Data Transfer)**이 별도의 큰 비중을 차지함을 보여준다.

## CPU에서의 추론은 여전히 중요하다

GPU/NPU가 주목받지만, 전 세계에는 여전히 압도적으로 많은 CPU가 존재한다. 기존 연구 상당수가 **단일 연산자(single operator) 가속**에만 집중했다면, 다음 연구는 **텐서 레벨 + 그래프 레벨 결합 최적화**를 CPU에 적용한다.

### Optimizing CNN Model Inference on CPUs — NeoCPU (USENIX ATC'19)

**1) 단일 스레드 최적화**

- **차원 순서(dimension ordering)**: 메모리 지역성(locality)에 유리하게 배치
- **레지스터 블로킹(register blocking)**: 벡터화(vectorization) 명령을 효율적으로 활용하도록 블록 크기 조정
- 블록 크기(x, y), 사용 레지스터 수(`reg_n`), 루프 언롤 전략(`unroll_ker`)을 **템플릿화**해 쉽게 설정 가능하게 구성

**2) 스레드 레벨 병렬화**

- 코어 수와 동일한 개수의 스레드로 구성된 커스텀 스레드 풀 사용

**3) 레이아웃 변환 제거(Layout Transformation Elimination)**

연산을 데이터 레이아웃 민감도에 따라 세 범주로 나눈다:

| 범주 | 예시 | 레이아웃 민감도 |
|---|---|---|
| Layout-oblivious | ReLU, Softmax 등 단항(unary) 연산 | 무관 |
| Layout-tolerant | Conv, BatchNorm, Pooling | 특정 레이아웃에서 더 유리하지만 유연 |
| Layout-dependent | Flatten, Reshape | 레이아웃에 강하게 의존 |

계산 그래프 상에서 레이아웃이 바뀌어야 하는 지점에만 `LayoutTransform` 노드를 삽입해 불필요한 변환을 최소화한다.

**4) 최적화 스킴 탐색: Local Search → Global Search**

- **Local Search**: Convolution 스케줄을 `(ic_bn, oc_bn, reg_n, unroll_ker)` 튜플로 표현. 예를 들어 채널 수가 64라면 `ic_bn/oc_bn` 후보를 `[32,16,8,4,2,1]`처럼 나열하고, `reg_n` 후보를 `[32,16,8,4,2]`에서, `unroll_ker`은 `[True, False]`에서 선택해 **모든 조합의 실행시간을 측정**한 뒤 실행시간 오름차순으로 정렬한 후보 리스트를 만든다.
- **Global Search**: 개별 연산 후보들을 조합해 **end-to-end 전체 그래프 관점에서 최적**의 조합을 선택한다.

결과적으로 NeoCPU는 다양한 클라우드·엣지 CPU에서 경쟁력 있는 성능과 확장성을 보였다.

## 이 장이 보여주는 공통 패턴

Ch.5(컴파일러)에서 본 "그래프 레벨 + 텐서/루프 레벨 최적화의 결합", Ch.6에서 본 "탐색 공간을 정의하고 비용 모델로 최적점을 찾는" 접근이, 엣지·CPU라는 구체적인 배포 환경에서도 그대로 반복된다는 점에 주목하자. **"어디서 실행할 것인가(엣지 vs 클라우드)"**와 **"어떻게 스케줄링할 것인가(local/global search)"**는 다음 장(Ch.8, NPU 매핑 문제)에서도 동일한 구조로 다시 등장한다.

## 시험·복습 체크포인트

- Neurosurgeon이 "레이어 단위 분할"을 선택한 이유는?
- Fused-layer level parallelization이 fine-grained parallelization보다 나은 이유는?
- SIEVE의 speculative CNN 구조가 해결하려는 문제는 무엇인가?
- NeoCPU가 연산을 layout-oblivious/tolerant/dependent로 분류하는 이유는?
- Local Search와 Global Search의 역할 차이는?
{% endraw %}

---

이전: [06. 메모리 최적화와 네트워크 프루닝](06-memory-optimization-and-network-pruning.md) · 다음: [08. NPU 아키텍처와 데이터플로우](08-npu-architecture-and-dataflow.md)
