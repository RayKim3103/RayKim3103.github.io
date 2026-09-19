---
layout: page
title: "04. 고속 수신기 (Receiver) 설계"
permalink: /grad_studies/high_speed_serial_link_circuits/04-high-speed-receivers/
sitemap: false
---

- **강의**: Lecture 4 — High-Speed Receivers

{% raw %}
## 개요

수신기(RX)가 채널을 통과해 손상된 신호로부터 원래의 디지털 데이터를 복원하려면 두 가지를 동시에 해내야 한다: **정확한 시점(time)에 신호를 포착(sampling)**하고, **그 값이 0인지 1인지 판별(detection)**해야 한다. 이 장은 이 두 임무를 가장 효율적으로 수행하는 회로 — **Regenerative Amplifier(대표적으로 StrongARM Latch)**를 중심으로 다룬다.

## 수신기의 두 가지 기본 임무

```text
Sampling (Timing 축)   : 연속 신호에서 심볼 경계를 정확히 구분 → 동기화(synchronization)
Detection (Voltage 축) : 작은 스윙을 Full Logic Level(0/1)로 판별 → 감도(sensitivity)
```

## 수신기 핵심 파라미터

| 파라미터 | 축 | 정의 | 영향 |
|---|---|---|---|
| **Sensitivity** | Voltage | 올바른 심볼 복원에 필요한 최소 입력 전압 | Eye Height 마진 |
| **Offset Voltage** | Voltage | 수신기 판정 임계값의 이상적 레벨로부터의 편차 | 유효 감도 열화 |
| **Aperture Time** | Time | 샘플링 시 실제로 값을 취하는 시간 창 | Eye Width 마진 |
| **Jitter** | Time | 클록/데이터 타이밍 불확실성 (Random + Deterministic) | 샘플링 타이밍 오차 |

이 네 가지가 결합해 **필요한 아이 오프닝(eye opening)** 크기를 결정한다 — Aperture Time과 지터의 합이 최소 아이 폭(width)을, Sensitivity와 Offset의 합이 최소 아이 높이(height)를 정한다.

### 통신이론 관점: AWGN 채널과 최적 임계값

디지털 통신에서 표준적으로 사용하는 채널 모델은 **AWGN(Additive White Gaussian Noise)** 채널이다. 두 인접 심볼 사이의 최적 판정 임계값은 그 중간점이며, 이때의 BER은 가우시안 노이즈 통계로 표현된다.

## 수신기 기준 전압(Reference) 생성 방법

| 방법 | 설명 | 장단점 |
|---|---|---|
| **RX 전원으로 로컬 생성** | 전압 분배기 + 필터 커패시터 | TX 조건(오프셋)과 무관하게 설정됨. 다중 RX 간 공유 가능하지만 노이즈 커플링 불균형 우려 |
| **TX에서 생성 후 전송** | SSO(Simultaneous Switching Output) 노이즈 취소 가능 | 추가 배선 필요 → 비현실적 |
| **암묵적 기준 (Differential/Bipolar/AC-coupled)** | "0"(차분 신호의 차이 = 0)이 곧 기준 | 별도 기준 전압 불필요 — 실무에서 선호 |

## 두 가지 수신기 구조

| 구조 | 방식 |
|---|---|
| **Amplify-and-Sample** | 전압 판별과 시간 판별을 분리 — Static amplifier + Flip-flop |
| **Clocked Amplifier (권장)** | 두 임무를 결합 — Regenerative Amplifier |

**Amplify-and-Sample의 문제**: 필요한 대역폭·이득을 얻으려면 다단(multi-stage) 증폭기가 필요하고, 특히 초반 스테이지들이 **큰 정적(static) 전력**을 소모한다.

## Regenerative Amplifier: 클록 기반 샘플링의 핵심 원리

두 증폭기를 루프로 연결하면 신호가 루프를 돌며 증폭되지만, "언제 들어오고 언제 나가는지"를 제어해야 한다 — 클록/스위치로 루프를 열고 닫는다.

### 3단계 동작

1. **Tracking Phase** (Clock Low): 내부 노드 $V(X)$가 $V_{in}$을 따라감(tracking). Regeneration은 OFF.
2. **Sampling Phase** (Clock Rising Edge): 클록 전환 순간 $V_{in}$ 값이 내부 노드에 샘플링됨 — 이 순간이 **Aperture Time**의 중심.
3. **Regeneration Phase** (Clock High): Positive Feedback(cross-coupled latch)이 작동해 작은 전압 차이를 지수적으로 증폭시켜 Full $V_{DD}$ 또는 GND로 판정(decision)이 완료된다.

이 방식은 **Static 전류가 거의 없어 Low Power + High Speed**를 동시에 달성한다 — Regenerative Amplifier는 본질적으로 **샘플러**이기도 하다 (좁은 시간 창 동안만 입력을 보고 증폭 → Sample-and-Amplify, clocked comparator의 기반).

## Aperture Time과 ISF(Impulse Sensitivity Function)

이상적인 샘플링은 델타 함수($\Gamma(\tau)=\delta(\tau)$) 형태로, 정확히 그 순간의 값만 반영하고 무한 대역폭을 가진다. 실제로는 유한한 시간 창(aperture time)이 존재한다.

$$
V_{sample} = \int_{-\infty}^{+\infty} \Gamma(\tau)\cdot V_{in}(\tau)\, d\tau
$$

- **Sampling switch의 aperture**: 트래킹 구간 동안 $V_{out}$은 RC-필터링된 $V_{in}$이며, 스위치가 열리는 시점 $t_s$ 직전에 도착한 입력이 가장 큰 영향을 준다 → $\Gamma(t)$는 $\tau=RC$인 "좌측 지수(left-exponential)" 형태.
- **Regenerative amp의 aperture**: 재생(regeneration)이 $t=t_s$에서 시작되면, 그 순간 도착한 입력이 최대 이득을 보고 이후 입력은 지수적으로 감쇠하는 이득을 본다 → "우측 지수(right-exponential)" 형태.
- 실제 $\Gamma(t)$는 이 둘을 합친 형태이며, 10%-80%-10% 기준으로 **Aperture Time**을 정의하고, 이를 푸리에 변환하면 **Sampling Bandwidth(-3dB)**를 얻는다.

## StrongARM Latch: 대표적인 Regenerative 비교기

### 구조

- Differential Pair (입력)
- Clocked Tail NMOS (동적 동작 → 정적 전류 거의 없음)
- Cross-coupled Latch (positive feedback으로 재생)
- Pre-charge PMOS (리셋)
- 뒤에 RS Latch(또는 NAND 기반) 연결 → 전체가 Flip-flop처럼 동작

### 4단계 동작

1. **Reset**: Clock Low → 상단 PMOS ON → 출력 노드 두 개 모두 $V_{DD}$로 pre-charge
2. **Sampling**: Clock Rising → Tail NMOS ON → 입력 차이($V_{IN+}-V_{IN-}$)에 따라 한쪽 노드가 더 빠르게 discharge (이 순간이 aperture time의 중심)
3. **Regeneration**: 임계 시점 $t_1$ (PMOS turn-on)에서 cross-coupled inverter가 positive feedback으로 증폭 시작

   $$
   G_R = \exp\left(\frac{t_2-t_1}{\tau_R}\right), \qquad \tau_R = \frac{C_{out}}{g_{m2,r}+g_{m3,r}}
   $$

   재생 시정수 $\tau_R$을 최소화할수록 이득과 감도가 개선된다.
4. **Decision**: 출력이 Full logic level로 안정화 → 뒤쪽 RS Latch가 값을 유지

### 구조의 진화

기본 regeneration amplifier 구조(Kobayashi) → bridging MOSFET 추가(Montanaro, 저항 감소로 재생 개선) → pre-charging MOSFET 추가(Wang)로 발전해왔다.

### RS Latch: 왜 필요하고 왜 개선되어야 하는가

StrongARM latch는 half clock cycle 동안 $V_{out+}=V_{out-}=V_{DD}$(무효 출력, invalid)를 만들어 **펄스 생성기(pulse generator)**처럼 동작한다 → 뒤에 **RS Latch**를 붙여 값을 올바르게 해석(유지)해야 한다.

| 방식 | 특징 |
|---|---|
| **Conventional RS Latch** | 상승 천이가 먼저, 하강 천이가 뒤따름 — 두 출력 중 하나가 항상 지연됨 |
| **Improved(Symmetric) RS Latch** | 대칭적인 pull-up/pull-down 경로로 상당한 speed-up. 평가(evaluation) 시 큰 driver 트랜지스터가 동시에 동작(keeper path는 비활성화), reset 상태에서는 작은 keeper cross-coupled inverter만 활성화되어 load capacitance를 최소화 |

Improved RS Latch는 StrongARM latch 지연시간을 약 2배 개선하며, 다른 플립플롭 토폴로지보다 우수한 지연 성능을 보인다.

## Non-Ideality와 대응

| Non-Ideality | 원인 | 영향 | 대응 |
|---|---|---|---|
| **Offset** | 소자 미스매치 (주로 입력쌍, $\Delta V_{TH}$·$\Delta I$) | Threshold shift → BER ↑ | W/L 증가, Capacitive/Current trimming, Background calibration |
| **Hysteresis** | Reset 불완전 (이전 데이터 잔여) | 이전 심볼이 다음 심볼에 영향 (data-dependent offset) | 더 강한 Reset, Bridging MOSFET, 추가 pre-charge |
| **Aperture Time 확대** | 기생 RC + 유한한 스위칭 속도 | 유효 샘플링 창 확대 | 기생 성분 최소화, 빠른 재생(regeneration) |

Offset은 레이아웃을 대칭으로 구성해도 랜덤 미스매치를 완전히 없앨 수 없으며, 입력쌍 미스매치가 가장 민감하다. Capacitive DAC(binary-weighted cap array)로 의도적 불균형을 만들거나 tail current를 조절하는 방식으로 offset을 보정(calibration)한다.

## Demultiplexing Receiver (Sub-rate Clocking)

전체 데이터 레이트를 그대로 클록으로 쓰는 대신(**Full-rate**), 클록 주파수를 데이터 레이트의 부분값으로 낮추고(**Half-rate, Quarter-rate**) 비교기 수를 늘리는 방식이다.

| 클록 방식 | Comparator 수 (예: 40Gbps) | 특징 |
|---|---|---|
| Full-rate | 1개 (40GHz 클록) | 클록 설계 매우 어려움 |
| Half-rate | 2개 (20GHz 클록) | - |
| Quarter-rate | 4개 (10GHz 클록) | 멀티페이즈 클록, 큰 입력 로딩, offset 이슈 필요 |

Sub-rate로 갈수록 비교기 개수가 늘고 클록 속도는 낮아져 설계가 쉬워지지만, **Clock-to-Q Delay**가 길면 retiming 마진이 부족해진다 — 특히 quarter-rate에서 마지막 path의 타이밍이 매우 타이트해진다.

## PAM4 Receiver

PAM4 신호는 4개의 레벨을 구분하기 위해 **3개의 데이터 샘플러**(임계값 $D_{levH}$, 중앙, $D_{levL}$)가 필요하다.

- 2-bit Flash ADC(3개 비교기) 구조로 PAM4 심볼을 판정한다.
- **Swept error sampler**로 PAM4 임계값을 적응(adaptation)시키고, **Edge sampler**는 CDR과 이퀄라이저 적응에 필요한 정보를 제공한다 (Roshan-Zamir, JSSC'13/'19).
- 비교기 임계값 적응(threshold adaptation): 백그라운드에서 지속적으로 slicer를 PAM4 아이의 중앙에 위치시키도록 조정하며, error sampler가 아이의 가장자리를 추적해 높이를 측정한다.

### PAM4가 Receiver 설계에 주는 부담

- NRZ: 임계값 1개 → PAM4: 임계값 3개 → 샘플러 개수 3배
- Quarter-rate PAM4 → 샘플러 총 12개(기본 4×3) → AFE 로딩 급증, 오프셋 캘리브레이션 복잡도 폭증

## 실무 종합 (강의 노트 기반)

- 고속(>10Gbps) 수신기의 핵심 회로는 **Strong-Arm Latch + RS Latch**가 사실상 표준이다.
- 설계 우선순위: (1) Low Offset(캘리브레이션 필수) → (2) 작은 Aperture Time + 낮은 지터 → (3) 낮은 Clock-to-Q Delay(특히 sub-rate) → (4) 낮은 전력·높은 대역폭.
- PAM4로 갈수록 샘플러 개수와 캘리브레이션 복잡도가 폭발적으로 증가한다.

## 시험·복습 체크포인트

- Sensitivity, Offset Voltage, Aperture Time, Jitter가 각각 아이 오프닝의 어느 축(height/width)에 영향을 주는가?
- Amplify-and-Sample 구조가 고속에서 기피되는 이유는?
- Regenerative Amplifier의 3단계(Tracking/Sampling/Regeneration) 동작과, 왜 이 구조가 Low Power인지 설명할 수 있는가?
- StrongARM Latch가 half clock cycle 동안 "무효 출력"을 내는 이유와, 이를 해결하기 위해 RS Latch가 필요한 이유는?
- Offset과 Hysteresis의 원인과 대응 방법의 차이는?
- Sub-rate(Full/Half/Quarter-rate) clocking에서 비교기 수와 클록 속도의 트레이드오프는?
- PAM4 Receiver가 NRZ Receiver 대비 왜 샘플러가 3배 이상 필요한가?
{% endraw %}

---

이전: [03. 고속 송신기 설계](03-high-speed-transmitters.md) · 다음: [05. 이퀄라이제이션 기초와 송신단 이퀄라이저(TX FFE)](05-equalization-basics-and-tx-equalization.md)
