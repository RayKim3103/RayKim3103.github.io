---
layout: page
title: "06. 수신단 이퀄라이저(CTLE·DFE)와 적응"
permalink: /grad_studies/high_speed_serial_link_circuits/06-rx-equalization-and-adaptation/
sitemap: false
---

- **강의**: Lecture 6 — RX EQ and Adaptations

{% raw %}
## 개요

Ch.5의 TX FFE는 "채널을 보지 못한 채 미리 왜곡시키는" 방식이라는 한계가 있다. 이 장은 채널을 통과한 신호를 직접 관찰할 수 있는 **RX 쪽 이퀄라이저**(CTLE, DFE)와, 채널 조건이 변할 때 이들을 자동으로 최적화하는 **적응(Adaptation) 알고리즘**을 다룬다.

## TX EQ vs RX EQ

| 항목 | TX Equalization (FFE) | RX Equalization (CTLE + DFE) |
|---|---|---|
| 보상 범위 | Pre-cursor + Post-cursor 모두 가능 | CTLE: 전반적 감소, DFE: Post-cursor 정교 제거 |
| 구현 난이도 | 비교적 쉬움 (데이터를 이미 알고 있음) | 더 어려움 (들어오는 아날로그 신호 처리) |
| 노이즈 | 증폭 없음 | CTLE는 노이즈 부스팅 가능 |
| 스윙 페널티 | De-Emphasis 시 스윙 감소 | 없음 (post-cursor cancellation) |
| 적응 | 어려움 (채널 출력을 관찰 불가) | 가능 (RX 출력 기반 피드백) |

실무에서는 **Tx FFE + Rx CTLE + Rx DFE**를 조합해 쓰는 것이 일반적이다: `TxFFE → Channel → CTLE → (FFE+DFE) → Slicers`.

## CTLE (Continuous-Time Linear Equalizer)

이상적으로는 채널 전달함수의 역함수가 필요하지만, 실질적으로는 **Nyquist 주파수까지 대역 제한된(band-limited) 선형 이퀄라이저**로 구현한다.

### Passive CTLE

R-C(또는 L)로 고역통과 전달함수를 구현해 채널 손실을 보상한다 — pre-cursor와 long-tail ISI를 모두 상쇄할 수 있다. **선형성이 매우 우수**하지만 Nyquist 주파수에서 이득이 없다(gain < 1).

### Active CTLE

RC degeneration을 가진 입력 증폭기로 Nyquist 주파수에서 이득이 있는 frequency peaking을 구현한다(1개의 zero + 2개의 pole) — 가장 흔한 설계 방식이다.

| 튜닝 파라미터 | 효과 |
|---|---|
| $C_S$ (degeneration 커패시터) 증가 | zero와 첫 pole이 이동 → peaking 증가 (DC gain 변화 없음) |
| $R_S$ (degeneration 저항) 증가 | zero 이동 → peaking 증가하지만 DC gain 감소 |

- DC Gain $= R_2/(R_1+R_2)$
- 출력 로딩(뒷단 샘플러 개수)이 대역폭을 제한 → CTLE 설계 난이도를 높인다.

### CTLE의 대가: 노이즈 부스팅

CTLE가 고주파를 부스트한다는 것은 **고주파 노이즈도 함께 증폭**한다는 뜻이다. 예를 들어 8Gb/s 데이터에서 fc=1GHz(CTLE 적용 채널)와 fc=4GHz(CTLE 없는 채널)를 비교하면, CTLE의 zero/pole 설정($f_Z=1$GHz, $f_{P1}=4$GHz, $f_{P2}=10$GHz)에 따라 노이즈 스펙트럼이 달라진다 — **peaking을 과도하게 키우면 노이즈 부스팅으로 오히려 손해**를 볼 수 있다.

## DFE (Decision Feedback Equalizer)

수신 신호에서 **post-cursor ISI를 빼는(subtract)** 비선형 이퀄라이저다.

$$
y_n = x_n - \sum_{k\ge1} w_k\cdot d_{n-k}
$$

| 장점 | 단점 |
|---|---|
| 메인 커서(Tx-EQ처럼) 감쇠 없음 | Pre-cursor는 제거 불가 |
| 노이즈 부스팅 없음(Rx-LE와 달리) | 이전 판정이 맞다고 가정 → **에러 전파(error propagation)** 가능 |

### DFE의 근본적 어려움: 타이밍 제약

DFE는 이전 비트를 알아야 현재 심볼의 ISI를 계산·제거할 수 있다.

```text
필요 조건: DFE 루프 지연시간 < 1 UI
10 Gbps → 100ps, 100 Gbps → 10ps 안에
  Sampling + Multiplication + Subtraction + Settling이 모두 끝나야 함
```

샘플러 속도(SA latch + RS latch)가 매우 중요하며, 신호가 A-D 경계를 두 번 건넌다는 근본적 어려움이 있다.

### Loop Unrolling (Speculative DFE)

**아이디어**: 이전 비트가 확정된 후 ISI를 빼는 대신, **가능한 모든 ISI 케이스에 대해 미리 비교**해두고 나중에 그중 하나를 선택한다 — carry-select adder와 유사한 발상이다. 전체 decision feedback 루프가 디지털 도메인에 있어 고속 동작이 가능해진다.

- **1-tap DFE**: 이전 비트가 0/1인 두 경우를 미리 계산 → MUX로 선택
- **2-tap DFE**: 4가지 경우를 미리 계산 (Slicer 임계값 = 가능한 ISI 값들의 집합)
- **N-tap DFE**: 슬라이서 개수가 $2^N$으로 **지수적으로 증가** — 탭 수가 늘수록 하드웨어 오버헤드가 폭증한다.

## Summer 구현: DFE의 핵심 블록

Summer는 입력과 weighted feedback ISI를 빼는 역할을 하며, ISI 상쇄를 위해 95% 이상의 정확도로 settle해야 한다.

| Summer 유형 | 구조 | 장점 | 단점 |
|---|---|---|---|
| **Resistive-Load Summer** | CML differential pair + 저항 부하 + tap 전류 | 구현 간단, 선형성 좋음 | Static current 큼, RC time constant로 대역폭 제한 |
| **Integrating Summer** | Clocked integrator (Reset → Integrate) | Static power 거의 없음, 전형적으로 약 3배 bias current 절감 | Settling time 관리 어려움 |

$$
\text{Integrating Summer swing} = \frac{I\times \Delta T}{C} \quad (\Delta T/C > R \text{일 때 유리})
$$

**Summer Merged at Sampler**: latency를 줄일 수 있지만, summation 노드 커패시턴스 증가로 속도가 제한된다.

## Interleaved DFE

Full-rate에서 1UI 제약이 너무 타이트하면, **Half-rate/Quarter-rate**로 인터리빙한다 — 클록 속도는 낮아지지만 Clock-to-Q Delay + Settling Time이 여전히 1UI 안에 들어와야 하는 것이 가장 critical한 제약으로 남는다. Quarter-rate는 레이아웃이 특히 복잡해진다.

## FIR + IIR DFE

FIR DFE는 탭마다 개별 가중치를 두므로 long-tail ISI(2번째 post-cursor 이후)를 처리하려면 탭 수가 많이 필요해 하드웨어 오버헤드가 커진다. **IIR feedback filter**로 매끄러운(smooth) long-tail ISI를 하나의 필터로 억제할 수 있다 — 단, 채널 응답이 "매끄러워야"(예: 온칩 배선, 실리콘 캐리어 배선) 효과적이다.

## 왜 이퀄라이저 적응(Adaptation)이 필요한가

채널 특성은 시스템마다 크게 다르고(예: 9인치 FR4 vs 26인치 FR4, via stub 유무), 온도 변화·노후화(aging)로 시간에 따라 드리프트하기도 한다 — 이퀄라이저는 **주기적으로 최적 설정에 맞춰 조정**되어야 한다. 탭 계수가 최적화되지 않으면 **Under-equalized**(부족) 또는 **Over-equalized**(과도) 상태가 되어 아이가 닫힌다.

### LMS(Least-Mean-Squares) Adaptation

평균제곱오차 $e^2[n]$를 줄이는 방향으로 탭 계수 $w[n]$을 조정한다(실제로는 순간 오차로 근사). 계수 $w[k]$는, $k$비트 이전 수신 신호 $y[n-k]$와 오차 $e[n]$가 **직교(orthogonal)**해질 때까지 조정된다 — $e[n]\cdot y[n-k]=0$이 되면 더 이상 $w[k]$를 바꿔도 오차를 줄일 수 없다는 뜻이다. 다만 순수 LMS는 $e[n]$과 $y[n-k]$를 고해상도로 측정해야 해서 구현이 어렵다.

### Sign-Sign LMS (SSLMS)

$e[n]$과 $y[n-k]$의 **부호(sign)만** 사용해 하드웨어를 단순화한다 — 가장 널리 쓰이는 실용적 구현이다.

$$
w_k[n+1] = w_k[n] + \mu_k\cdot \text{sign}(\text{Data}[n-k])\cdot \text{sign}(\text{Err}[n])
$$

$$
dLev[n+1] = dLev[n] + \mu_{dLev}\cdot \text{sign}(\text{Err}[n]) \quad (\text{if } \text{Data}[n]>0)
$$

Error sampler는 적절한 데이터 레벨(dLev) 기준이 필요하므로 dLev 자체도 함께 적응시켜야 한다.

## 그 외 Adaptation 방법들

| 방법 | 원리 |
|---|---|
| **Power Balancing** (Choi, JSSC'04) | 이퀄라이저 출력(A)과 limiter 출력(B)의 전력을 비교 — DC gain은 저주파 전력을, 고주파 gain은 고주파 전력을 맞추도록 조정 |
| **Spectrum Balancing** (Lee, ISSCC'06) | 이퀄라이저 출력 스펙트럼을 이상적인 랜덤 바이너리 데이터의 스펙트럼과 같아지도록 조정 |
| **EOM 기반** (Won, TCAS-I'17) | Stochastic Sigma-Tracking EOM(SSEOM) — 양/음 데이터 레벨 기준에서 1-시그마 떨어진 전압을 찾아, 유효 아이 오프닝을 최대화하도록 DFE 탭을 조정 |
| **BER 기반** (Chen, JSSC'08 / Son, JSSC'13) | Gradient descent처럼 동작. 2단계: (1) SBR 측정 기반 coarse adaptation(training pattern 사용, 초기 아이 확보), (2) stochastic hill climbing 기반 fine adaptation(기존 hill climbing 대비 적응 시간 단축) |
| **Genetic Algorithm** (Shahramian, ISSCC'19) | 전통적 gradient descent의 suboptimal 수렴을 회피. Cost function: vertical eye opening |
| **Edge 기반** (Huang, ISSCC'11 / Shahramian, JSSC'16 / Shim, TCASII'22) | 2× 오버샘플링의 edge sample을 활용해 error sample 없이도 IIR DFE 적응 가능. CDR과 edge sampler를 공유해 오버헤드 최소화 |

### Genetic Adaptation Algorithm 동작 단계

1. 무작위 초기 계수(children) 생성 (예: 세대당 15개)
2. 상위 3개(부모) 선정 → 부모의 산술 평균으로 진화(evolution)
3. 부모를 중심으로 제한된 범위 내 controlled mutation
4. 전체 성능 곡면에서 무작위로 뽑는 random mutation
5. 상위 3개(survivors)를 다음 세대로 → 2~5단계 반복

20회 실행 × 세대당 80회 반복, 11~21dB 채널 손실 조건에서도 안정적으로 수렴함이 실험적으로 확인되었다.

## 실무 종합 (강의 노트 기반)

| 항목 | 권장 |
|---|---|
| 고속 Rx(25G+ NRZ / 56G+ PAM4) | Tx FFE(2~4탭) + Rx CTLE + Rx DFE 조합이 표준 |
| DFE 타이밍 해법 | Speculative(Loop-unrolling) DFE 또는 Interleaved DFE 필수 |
| Summer 선택 | Low-power → Integrating Summer, High-speed → Resistive-Load |
| Adaptation 기본값 | Sign-Sign LMS(안정적, 구현 쉬움), Edge-based(CDR과 공유로 오버헤드 최소), Genetic/Stochastic(local optima 문제 해결) |
| PAM4 | 임계값 3개 + DFE 탭 수 증가 → 적응 복잡도 급증 |

## 시험·복습 체크포인트

- CTLE가 노이즈를 증폭시키는 근본적인 이유는? Peaking을 과도하게 키우면 왜 손해인가?
- DFE의 pre-cursor 상쇄 불가능성과 error propagation 문제를 설명할 수 있는가?
- Loop Unrolling DFE가 타이밍 제약을 해결하는 원리와, 탭 수 증가에 따른 하드웨어 비용(지수 증가)의 관계는?
- Resistive-Load Summer와 Integrating Summer의 트레이드오프는?
- Sign-Sign LMS 업데이트 식에서 $\mu_k$, $\text{sign}(\text{Data})$, $\text{sign}(\text{Err})$이 각각 하는 역할은?
- Genetic Algorithm 기반 적응이 gradient descent 대비 갖는 장점은?
- Edge 기반 적응이 CDR과 하드웨어를 공유할 수 있는 이유는?
{% endraw %}

---

이전: [05. 이퀄라이제이션 기초와 송신단 이퀄라이저(TX FFE)](05-equalization-basics-and-tx-equalization.md) · 다음: [07. 클록 데이터 복원 (Clock and Data Recovery)](07-clock-and-data-recovery.md)
