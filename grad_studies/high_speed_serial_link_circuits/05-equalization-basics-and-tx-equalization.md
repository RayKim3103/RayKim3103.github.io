---
layout: page
title: "05. 이퀄라이제이션 기초와 송신단 이퀄라이저(TX FFE)"
permalink: /grad_studies/high_speed_serial_link_circuits/05-equalization-basics-and-tx-equalization/
sitemap: false
---

- **강의**: Lecture 5 — Equalization Basics and TX EQ

{% raw %}
## 개요

Ch.2에서 본 채널 손실은 결국 **Intersymbol Interference(ISI)**라는 형태로 수신 신호를 왜곡시킨다. 이 장은 (1) ISI를 정량적으로 분석하는 도구인 **Single-Bit Response(SBR)**, (2) 이를 이용한 worst-case 아이 높이 계산법, (3) 채널 응답의 역함수를 만들어 이 왜곡을 상쇄시키는 **이퀄라이제이션(Equalization)**의 개념, 그리고 (4) 그 첫 구현체인 **TX FFE(Feed-Forward Equalizer)**를 다룬다.

## Single-Bit Response (SBR)와 ISI

채널 특성을 대표하는 도구가 **Single-Bit Response**다.

```text
φ(t): TX에서 보내는 단일 비트 펄스
h(t): 채널 임펄스 응답 (S-parameter 모델로부터 시뮬레이션)
p(t) = φ(t) * h(t): RX 입력에 도착한 단일 비트 응답 (channel output)
p[n]: p(t)를 데이터 타이밍 시점에서 샘플링한 값
```

채널이 선형시불변(LTI) 시스템이라면, 비트열 $\{b_k\}$에 대한 수신 신호는:

$$
y[n] = \sum_{k=-\infty}^{\infty} b_{n-k}\cdot p[k] + N[n] = \underbrace{b_n\cdot p[0]}_{\text{Main Cursor(원하는 값)}} + \underbrace{\sum_{k\ne 0} b_{n-k}\cdot p[k]}_{\text{ISI(pre/post-cursor)}} + \underbrace{N[n]}_{\text{Noise}}
$$

여기서 $k>0$은 **post-cursor**(이후 심볼 영향), $k<0$은 **pre-cursor**(이전 심볼 영향)이다.

### ISI 발생 예시

TX가 "101…"을 전송할 때, 가운데 "0"은 이전 심볼의 **trailing ISI**(예: 0.2)와 다음 심볼의 **leading ISI**(예: 0.1)를 합쳐 총 0.3만큼 왜곡될 수 있다 — 기준값이 0.3보다 낮으면 이 "0"이 "1"로 잘못 판정되는 **비트 에러**가 발생한다.

또한 ISI는 전압뿐 아니라 시간축에도 영향을 준다: 연속된 비트 뒤에 전이(transition)가 오면(예: "110") 엣지가 뒤로 밀리고, 교대 패턴 뒤에 전이가 오면(예: "010") 엣지가 앞으로 당겨진다 — 이것이 **data-dependent jitter**다. 채널 손실이 커질수록 아이 히스토그램이 여러 갈래로 갈라진다.

## Worst-Case Eye Height (Peak Distortion Analysis)

노이즈가 0이라고 가정하면, 펄스 응답만으로 **worst-case 아이 높이**를 추정할 수 있다.

- **Worst-case "1"**: 메인 커서에 모든 음(negative)의 ISI가 더해진 경우

$$
y^1[n] = p[0] - \sum_{k\ne0}|p[k]|
$$

- **Worst-case "0"**: 메인 커서에 모든 양(positive)의 ISI가 더해진 경우

$$
y^0[n] = -p[0] + \sum_{k\ne0}|p[k]|
$$

대칭적인 "1"/"0" 펄스(선형성) 가정 하에 worst-case 아이 높이는:

$$
y^1[n] - y^0[n] = 2\cdot y^1[n] = 2\left(p[0]-\sum_{k\ne0}|p[k]|\right)
$$

**예시**: $p[0]=0.540$, 음의 항 합 $=-0.007$, 양의 항 합 $=0.389$일 때, Worst-case eye height $= 2(0.540-0.007-0.389) = 0.288$.

### ISI-Induced Jitter

전이(edge) 위치의 최대 변동폭(peak-to-peak)도 펄스 응답으로부터 계산할 수 있다.

$$
\Delta y_{edge,pp} = \sum_k |p[k+0.5]|, \qquad \Delta jitter_{ISI,pp} = \frac{\Delta y_{edge,pp}}{dy/dt|_{t=T_e}}
$$

### Worst-Case Bit Pattern

펄스 응답 $p[n]=[\ldots,p_{-2},p_{-1},p_0,p_1,p_2,\ldots]$가 주어지면, worst-case 비트 패턴은 **각 위치의 부호를 해당 펄스 값과 반대로** 선택하면 된다: $[\ldots,-\text{sign}(p_2),1,-\text{sign}(p_{-1}),\ldots]$.

**예시**: $p[-2\sim4]=[-0.1,0.2,1,0.3,0.2,-0.1,0.05]$일 때, worst-case 패턴은 $[-1,+1,-1,-1,1,-1,1]$이고, worst-case eye height $=2(1-0.95)=0.1$.

## Channel Equalization: 개념과 분류

이퀄라이저는 채널의 주파수 의존적 손실을 보상해, 전체 응답을 "평탄(flat)"하게 만드는 것이 목표다.

```text
dB                dB                    dB
 \                 /\                   ___
  \___     +      /  \        =      __/
Channel      Equalization      After Equalization
```

| 분류 기준 | 옵션 |
|---|---|
| 위치 | On-chip(Pre/De-emphasis, CTLE, FFE, DFE) vs Off-chip(보드/케이블 이퀄라이제이션) |
| 방향 | TX(Pre/De-emphasis, FFE) vs RX(CTLE, DFE, FIR/IIR filter) |
| 도메인 | Continuous-Time(CTLE, IIR, 아날로그) vs Digitally-Sampled(Pre/De-emphasis, DFE, FIR, 디지털) |

전형적인 조합: `TxFFE → Channel → CTLE → FFE+DFE → Slicers`.

### Equalization의 효과 (관찰)

- 2-tap TX EQ만으로도 큰 초기 성능 향상이 있다.
- TX EQ만으로는 2-tap과 4-tap 사이에 큰 차이가 없는 경우가 많다.
- **RX 이퀄라이제이션, 특히 DFE**가 추가적인 성능 개선을 이끈다.
- 단, 빠른 DFE는 critical timing path, 전력·면적 제약 때문에 구현이 어렵다 (Ch.6에서 상세).

## TX Equalization: FFE(Feed-Forward Equalizer)

TX 이퀄라이제이션은 보통 **FFE(=FIR filter)**를 사용한다 — 전송할 펄스를 미리 왜곡(pre-distort)시켜 채널 왜곡을 상쇄시키며, pre-cursor와 post-cursor ISI를 모두 상쇄할 수 있다.

| 장점 | 단점 |
|---|---|
| 구현이 단순 | 피크 전력 제한으로 저주파 성분 감쇠 |
| Pre-cursor ISI 상쇄 가능 | 적응(adaptation)을 위해 back-channel 필요 |
| 노이즈를 증폭시키지 않음 | - |

### Pre-Emphasis (2-tap FIR)

$$
w(z^{-1}) = 1-\alpha\cdot z^{-1}
$$

$\alpha$는 2번째 탭 계수(첫 post-cursor 탭)이며, 전류 비율(current-mode) 또는 세그먼트 비율(voltage-mode)로 결정된다. 고주파 성분을 부스트하지만, 전력 요구가 크고 crosstalk를 악화시킨다.

### Peak Swing Constraint와 De-Emphasis

피크 스윙 한계가 1이고 2-tap FIR $w(z^{-1})=1-\alpha z^{-1}$을 적용한다고 하면, $x[n]=[\ldots,-1,+1,\ldots]$에 대해 $y[0]=1+\alpha>1$이 되어 **피크 스윙 한계를 위반**한다.

이를 해결하려면 메인 커서 $w[0]$ 자체를 줄여야 한다 — **고주파를 부스트하는 대신 저주파를 감쇠**시키는 것이다.

$$
\sum_k|w[k]|\le W_{max}, \qquad w[0]=W_{max}-\sum_{k\ne0}|w[k]|
$$

$$
w(z^{-1})=1-\alpha z^{-1} \;\rightarrow\; w(z^{-1})=(1-\alpha)-\alpha z^{-1}
$$

이렇게 하면 $y[0]=(1-\alpha)+\alpha=1\le1$로 스윙 제약을 만족한다 — 이것이 **De-Emphasis**다.

### 주파수 영역 해석

10Gb/s용 3-tap FIR $w(z^{-1})=-0.131+0.595z^{-1}-0.274z^{-2}$를 예로 들면, DC($f=0$)에서 $|w|=0.190$(−14.4dB), Nyquist 주파수에서 $|w|=1$(0dB)로 — **14.4dB의 frequency peaking**, 즉 DC를 −14.4dB만큼 감쇠시키고 Nyquist 주파수는 0dB로 그대로 통과시킨다.

### TX Equalization 설계 예제

채널 $p[-2\sim4]=[-0.1,0.2,1,0.3,0.2,-0.1,0.05]$에 대해, worst-case 아이 오프닝을 최대화하는 2-tap 이퀄라이저 $w(z^{-1})=(1-\alpha)+\alpha z^{-1}$의 계수를 구하는 문제는, 등화 후 pre-cursor 항들의 절댓값 합과 post-cursor 항들의 제곱합을 최소화하는 $\alpha$를 찾는 최적화 문제로 귀결된다.

## FIR 이퀄라이저 구현

대개 TX에서 구현된다 — 지연(delay)이 단순히 플립플롭만 있으면 되기 때문이다.

| 구현 방식 | 특징 |
|---|---|
| **Direct FIR** | 각 탭마다 병렬 출력 드라이버. 계수가 채널/온도/공정에 따라 가변적, 탭 수가 많을수록 출력 커패시턴스 증가 |
| **Segmented DAC** | 최소 크기 출력 트랜지스터로 peak 출력 전류 처리, 낮은 출력 커패시턴스, 다만 전력·복잡도 증가(mapping table/RAM 필요) |
| **Current-Mode** | $w(z^{-1})=1-\alpha z^{-1}$, $\alpha$는 전류비($I_1/I_0$)로 결정. 전류-합산(current-summing) DAC → 전류 소모가 $I_0$에서 $I_0+I_1$로 증가하지만 구현이 쉬움 |
| **Voltage-Mode** | MOSFET을 직렬 임피던스($R_{on}$)로 모델. 전압 분배기 + GND shunt 경로로 FIR에 필요한 여러 전압 레벨을 임피던스 제어와 함께 구현. $R_{on}$의 비선형성, 탭 해상도가 커질수록 복잡해지는 pre-driver 로직이 단점 |

### Voltage-Mode 구현 상세: 임피던스와 계수의 독립 제어

M. Kossel(JSSC'08) 구조는 패시브 저항과 MOSFET을 결합해 탭 계수와 임피던스 변동을 완화한다. 전체 드라이버 폭은 출력 임피던스에 맞게 제약되고, 계수는 드라이버 세그먼트 비율로 결정된다.

$$
\text{De-emphasis [dB]} = 20\log\left(\frac{I-J}{I+J}\right)
$$

각 슬라이스가 완전한 de-emphasis 세트를 포함하도록 설계하면 이퀄라이제이션과 임피던스 튜닝을 독립적으로 조정할 수 있다.

### Relaxed Impedance Matched Equalization

- **Impedance matched EQ**: 전원-접지 간 짧은 전류 경로가 생겨 전력 요구가 증가한다.
- **Relaxed impedance matched EQ**: 신호 반사를 일부 감수하는 대신 짧은 전류 경로를 제거해 전류 소모를 줄인다. 전이(transition) 비트는 50Ω 임피던스를 유지하지만 비전이 비트는 더 높은 임피던스를 가진다.

### Time-Domain Equalizer (PWM)

전압 기반 de-emphasis 대신 **펄스폭 변조(PWM)**로 이퀄라이제이션을 구현하는 방식이다. Duty-cycle 비율 $D$를 0.5~1 사이에서 조절해 ISI를 억제하며, 전압 기반 접근과 달리 **Nyquist 주파수를 넘어서까지 이퀄라이제이션 효과가 확장**된다.

| 장점 | 단점 |
|---|---|
| 출력 스윙에 따른 드라이버 임피던스의 비선형 의존성 제거 | 좁은 펄스 생성이 공정 대역폭 한계에 부딪힘 |
| 종단 임피던스를 de-emphasis 양·출력 스윙과 분리(decouple) | - |

## 실무 종합 (강의 노트 기반)

- 고속 SerDes(25G+): **TX FFE 2~4 탭**이 표준으로 pre/post-cursor를 보상한다.
- FFE의 강점은 pre-cursor까지 보상 가능하고 노이즈를 증폭하지 않는다는 점이지만, 적응(adaptation)은 주로 RX 쪽에서 이뤄진다 — TX는 채널 출력을 직접 관찰할 수 없기 때문이다.
- PAM4로 갈수록 FFE 탭 수와 선형성(RLM 관리)이 더욱 중요해진다.

## 시험·복습 체크포인트

- Single-Bit Response의 pre-cursor/main cursor/post-cursor가 각각 무엇을 의미하는가?
- Worst-case eye height 공식 $2(p[0]-\sum_{k\ne0}|p[k]|)$이 유도되는 논리를 설명할 수 있는가?
- Pre-Emphasis와 De-Emphasis의 차이, 그리고 De-Emphasis가 필요한 이유(peak swing constraint)는?
- Current-mode와 Voltage-mode FFE 구현의 트레이드오프는?
- PWM 기반 Time-Domain Equalizer가 전압 기반 방식과 다른 근본적인 이점은 무엇인가?
{% endraw %}

---

이전: [04. 고속 수신기 설계](04-high-speed-receivers.md) · 다음: [06. 수신단 이퀄라이저(CTLE·DFE)와 적응](06-rx-equalization-and-adaptation.md)
