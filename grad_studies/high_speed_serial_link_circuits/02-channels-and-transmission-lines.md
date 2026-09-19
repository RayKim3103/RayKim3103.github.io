---
layout: page
title: "02. 채널과 전송선로"
permalink: /grad_studies/high_speed_serial_link_circuits/02-channels-and-transmission-lines/
sitemap: false
---

- **강의**: Lecture 2 — Channels and Transmission Lines

{% raw %}
## 개요

TX와 RX 사이에 놓인 "채널(channel)"은 단순한 전선이 아니다. 이 장은 채널을 구성하는 실제 물리적 요소들, 고주파에서 전선을 전송선로(transmission line)로 다뤄야 하는 이유, 두 가지 손실 메커니즘(저항성/유전체 손실), 그리고 반사(reflection)와 종단(termination) 이론까지 — **Signal Integrity(SI)**의 물리적 기반 전체를 다룬다.

## 전기 채널을 구성하는 이종(heterogeneous) 요소들

채널은 "케이블 하나"가 아니라, TX에서 RX까지 신호가 거치는 모든 물리적 경로다.

| 구성 요소 | 설명 | 고속 링크에서의 영향 |
|---|---|---|
| **IC Package** | 칩을 PCB에 연결 | Wire Bonding(저렴)은 인덕턴스·손실 증가, Flip-Chip은 우수한 SI |
| **Chip-on-Board (COB)** | 다이를 PCB에 직접 부착 | 패키지 기생 성분 최소화 |
| **고급 패키지 (MCM, Chiplet, 2.5D/3D)** | 실리콘 인터포저, Die-to-Die 연결 | 초단거리·초고대역폭 (예: Apple UltraFusion — 10,000+ 신호로 2.5TB/s) |
| **PCB Trace** | Stripline/Microstrip 형태의 신호선 | 임피던스 매칭이 폭/간격에 민감. FR-4(저렴, 손실 큼) vs Rogers(저손실, 고가) |
| **Via** | PCB 레이어 간 연결 | Stub으로 인한 공진·반사·손실 (Back-drilling으로 완화) |
| **Connector** | 보드-보드/백플레인 연결 | 임피던스 불연속 → 반사·EMI의 주요 원인 |

### 도달 거리(Reach)에 따른 채널 분류 (CEI 기준)

Common Electrical I/O(OIF-CEI) 표준은 손실 정도에 따라 채널을 분류한다.

| Reach 유형 | 전형적 손실 | 용도 예시 |
|---|---|---|
| Ultra-Short-Reach (USR) | < 5~10 dB | Die-to-Die, Chiplet |
| Very-Short-Reach (VSR) | ~10 dB | Chip-to-Module |
| Medium-Reach (MR) | ~20 dB | Chip-to-Chip (라인카드) |
| Long-Reach (LR) | 30~40+ dB | 백플레인 |

손실이 클수록 이퀄라이저(FFE/CTLE/DFE, Ch.5~6)를 더 강력하게 설계해야 한다 — **채널 설계와 이퀄라이저 설계는 항상 함께 고려해야 하는 짝**이다.

## 전기 채널을 전송선로로 다뤄야 하는 이유

저속·고SNR 환경에서는 전선을 단순히 "단락 회로(등전위)"로 취급해도 충분하다 (Ideal Model — 전선 전체가 동일 전위, 모든 지점에서 즉시 변화, 오디오 주파수 대역(~kHz)에서만 유효). 하지만 고주파에서는 신호가 "전파(propagate)"되며, 구동단과 수신단의 전위가 분산된 RLGC 성분 때문에 서로 달라진다 → **전송선로(Transmission Line)** 모델이 필요하다.

### Lumped vs Distributed

| 가정 | 조건 |
|---|---|
| **Lumped(집중정수) 모델** | 회로의 물리적 크기가 작아 연결선 양단의 전압·전류가 균일하다고 볼 수 있을 때 — 전원선, DC bias, 스캔 비트 등 |
| **Distributed(분포정수) 모델** | 짧은 신호선·저임피던스 짧은 TL 구간 → lumped 커패시터로 단순화 가능. On-chip DC 전원선 → lumped 저항. Off-chip AC 전원 도체 또는 짧은 고임피던스 TL 구간 → lumped 인덕터로 단순화 가능 |

### RC 전송선로 (On-chip 배선)

온칩 배선 대부분은 RC 전송선로로 모델링된다.

$$
\frac{\partial^2 V}{\partial x^2} = RC\frac{\partial V}{\partial t}
$$

지연시간과 라이즈 타임은 배선 길이의 **제곱**에 비례한다.

$$
\text{Delay: } t_d = 0.4 d^2 RC, \qquad \text{Rise time: } t_r = d^2 RC
$$

예를 들어 $0.6\mu m$ 정사각형 온칩 배선($R=0.12\,\Omega/\mu m$, $C=0.16\,fF/\mu m$)에서 10mm 길이의 배선은 지연시간 0.8ns, 라이즈 타임 2ns에 달한다 — **느린 라이즈 타임은 작은 전압 노이즈를 큰 지터로 변환**시킨다는 점이 중요하다.

### RLGC 전송선로 (General Case)

$$
\frac{\partial V(x,t)}{\partial x} = -RI(x,t) - L\frac{\partial I(x,t)}{\partial t}, \qquad \frac{\partial I(x,t)}{\partial x} = -GV(x,t) - C\frac{\partial V(x,t)}{\partial t}
$$

무한히 긴 선로에서 특성 임피던스는 다음과 같다.

$$
Z_0 = \sqrt{\frac{R+j\omega L}{G+j\omega C}} \; (\Omega)
$$

일반적으로 $Z_0$는 복소수이며 주파수에 의존한다.

### 주파수 영역 해와 전파 상수

$$
V(s,x) = V(s,0)\exp(-\gamma x), \qquad \gamma = \sqrt{(G+j\omega C)(R+j\omega L)} = \alpha + j\beta \; (m^{-1})
$$

- **실수부 $\alpha$**: 거리당 감쇠 (Np/m, $1\,\text{Np} = 8.686\,\text{dB}$)
- **허수부 $\beta$**: 거리당 위상 변화 (rad/m)

## 채널 손실의 두 가지 원인 (주파수 의존적)

손실 있는 전송선로에서:

$$
\alpha = \frac{R}{2Z_0} + \frac{GZ_0}{2} + j\omega\sqrt{LC} = \alpha_R + \alpha_D + j\omega\sqrt{LC}
$$

$$
\alpha_R = \frac{R}{2Z_0} \text{ (Resistive loss)}, \qquad \alpha_D = \frac{GZ_0}{2} \text{ (Dielectric loss)}
$$

| 손실 종류 | 원인 | 주파수 의존성 |
|---|---|---|
| **Resistive Loss ($\alpha_R$)** | Skin Effect (표피 효과) | $\propto \sqrt{f}$ (중저주파 지배적) |
| **Dielectric Loss ($\alpha_D$)** | Dielectric Absorption (유전체 흡수, $\tan\delta$) | $\propto f$ (고주파 지배적) |

### Skin Effect (Resistive Loss)

고주파 전류는 도체 표면으로 몰리는 경향이 있다 — 전류 밀도가 도체 깊이에 따라 지수적으로 감소한다.

$$
J = J_S e^{-d/\delta}, \qquad \delta = \sqrt{\frac{\rho}{\pi f \mu}}
$$

여기서 $\delta$는 **표피 깊이(skin depth)**로, 전류가 원래 값의 $e^{-1}$로 떨어지는 깊이다. 실효 도체 단면적이 줄어들어 저항이 증가한다. 표피 깊이가 도체 반경/두께의 절반과 같아지는 임계 주파수 $f_s$ 이상에서는 $R(f) \propto \sqrt{f}$로 증가한다.

### Dielectric Loss (Dielectric Absorption)

교류 전기장이 유전체 원자를 회전시켜 신호 에너지를 열로 흡수한다. 재료 고유 상수인 **손실 탄젠트(loss tangent)**로 표현된다.

$$
\tan\delta_D = \frac{G}{\omega C}, \qquad \alpha_D = \pi f \tan\delta_D \sqrt{LC}
$$

유전체 손실은 **주파수에 정비례**하므로, 고주파로 갈수록 저항성 손실보다 지배적이 된다.

### PCB 재료 선택의 트레이드오프

| 재료 | 손실 탄젠트 ($\tan\delta$) | 특징 |
|---|---|---|
| FR-4 | ≈ 0.02 | 저렴하지만 손실 큼 → Long-reach에 불리 |
| Rogers RO4000 | ≈ 0.0037 | 저손실, 고주파/장거리 적합, 고가 |
| Teflon | ≈ 0.001 | 매우 저손실 |

## 전송선로 구동과 반사(Reflection)

### 무한히 긴 선로

$V_S$가 소스 저항 $R_S$를 통해 무한히 긴 전송선로($Z_0$)를 구동할 때, 전압 분배기처럼 동작한다.

$$
V_F = \frac{Z_0}{R_S+Z_0}V_S, \qquad I_F = \frac{V_F}{Z_0}
$$

### 유한한 선로 — Open/Short Load에서의 반사

신호가 부하 끝에 도달했을 때, 부하가 신호를 완전히 흡수하지 못하면 **반사파**가 발생한다.

| Load 조건 | 반사계수 $\Gamma_L$ | 부하 전압 | 특징 |
|---|---|---|---|
| **Matched** ($Z_L=Z_0$) | 0 | $V_F$ | 반사 없음 — 가장 이상적 |
| **Open** ($Z_L=\infty$) | +1 | $2V_F$ (전압 두 배) | 전체 반사, 링잉(ringing) 심함 |
| **Short** ($Z_L=0$) | -1 | 0 | 전체 반사, 전압 상쇄 |

반사가 소스단에 도달했을 때, $R_S \ne Z_0$이면 다시 반사가 일어나고 ($R_S = Z_0$일 때만 소스에서 흡수된다 — **source termination**).

### 일반화된 반사계수와 전달계수

$$
\Gamma_L = \frac{Z_L-Z_0}{Z_L+Z_0}, \qquad \Gamma_S = \frac{R_S-Z_0}{R_S+Z_0}, \qquad \tau = \frac{2Z_L}{Z_L+Z_0}
$$

$Z_L=Z_0$이면 $\Gamma_L=0$, $\tau=1$ — 완벽한 전달.

### 종단(Termination)의 세 가지 방식

| 종단 방식 | 매칭 위치 | 장점 | 단점 |
|---|---|---|---|
| **Source Termination** | TX 쪽 ($R_S \approx Z_0$) | 간단, RX 쪽 부품 최소 | 부하 쪽 반사 가능(한 번) |
| **Receiver Termination** | RX 쪽 ($R_L \approx Z_0$) | TX 쪽 부담 적음 | 소스 쪽 반사 가능 |
| **Double Termination** | 양쪽 모두 | 반사 거의 0, 최고의 SI | 신호 스윙이 절반으로 감소 → 전력 소모 4배(∵ $V^2/R$) 필요 |

대부분의 고속 SerDes(PCIe, USB, Ethernet)는 SI를 위해 **Double Termination**을 채택한다.

### Lattice(Bounce) Diagram: 부정합(Mismatched) 예제

$Z_0=50\Omega$, $R_S=400\Omega$, $R_L=600\Omega$, $t_d=1\text{ns}$, $V_S=1\text{V}$ 스텝인 경우:

$$
\Gamma_{rL} = \frac{Z_T-Z_0}{Z_T+Z_0} = 0.846, \qquad \Gamma_{rS} = \frac{Z_S-Z_0}{Z_S+Z_0} = 0.778
$$

양쪽 모두 부정합이므로 신호가 무한히 왕복 반사하며(실제로는 손실 때문에 점차 감쇠) 계단식으로 서서히 목표값에 수렴한다 — 이 **링잉(ringing)**이 실제 데이터/클록 전송에서는 **data-dependent jitter + ISI**로 나타나 BER을 폭증시킨다.

## 채널 모델링과 측정

| 방법 | 설명 | 장점 | 단점 |
|---|---|---|---|
| **EM 시뮬레이션** (Ansys HFSS 등) | 3D 물리 구조 입력 → S-parameter 추출 | 설계 전 예측 가능 | 모델 정확도 의존적 |
| **TDR** (Time-Domain Reflectometry) | Step pulse를 보내고 반사를 관찰 | 임피던스 불연속 위치 파악 쉬움 | 정확도 낮음, 양방향 측정 어려움 |
| **VNA** (Vector Network Analyzer) | 주파수 스윕 소스로 S-parameter 직접 측정 | 가장 정확, 직관적 | 장비 고가, 캘리브레이션 필요 |

### S-Parameter

각 포트에서 입사파와 반사파의 비율로 정의된다.

$$
S_{11} = \left.\frac{B_1}{A_1}\right|_{A_2=0} \text{(반사계수, 매칭)}, \qquad S_{21} = \left.\frac{B_2}{A_1}\right|_{A_2=0} \text{(전달계수, 삽입손실)}
$$

- **$S_{11}$ (Return Loss)**: 임피던스 불일치 정도
- **$S_{21}$ (Insertion Loss)**: 채널 손실(dB) — 실측 예시로 DisplayPort 케이블의 경우 길이가 길어질수록 $S_{21}$이 예상대로 더 크게 감쇠한다.

## 실무 설계 흐름 요약

```text
1. Channel S-Parameter 측정/시뮬레이션 → Loss(S21) & Reflection(S11) 확인
2. Termination 결정 (대부분 Double Termination)
3. Tx Driver / Equalizer 설계 (Ch.3, Ch.5)
4. Rx Sampler / CDR / Equalizer 설계 (Ch.4, Ch.6, Ch.7)
```

좋은 Signal Integrity란 결국 **Loss 최소화(→ 이퀄라이저로 보상) + Reflection 최소화(→ Termination과 임피던스 연속성으로 억제)**라는 두 축으로 요약된다. 연구 트렌드로는 Chiplet/Die-to-Die 같은 Ultra-short reach(손실 <10dB) 환경에서는 이퀄라이저 부담이 줄어드는 대신 대역폭 요구는 폭발적으로 커지는 방향으로 가고 있다.

## 시험·복습 체크포인트

- Lumped 모델과 Distributed(전송선로) 모델을 구분하는 기준은 무엇인가?
- Resistive loss($\alpha_R \propto \sqrt f$)와 Dielectric loss($\alpha_D \propto f$)의 물리적 원인과 주파수 의존성 차이를 설명할 수 있는가?
- Open load와 Short load에서 반사계수가 각각 +1, -1이 되는 이유는?
- Source/Receiver/Double Termination의 트레이드오프(반사 억제 vs 전력)는?
- $S_{11}$과 $S_{21}$이 각각 무엇을 나타내는가?
- TDR과 VNA의 측정 원리 차이와, 각각이 유리한 상황은?
{% endraw %}

---

이전: [01. 고속 직렬 링크 개관](01-high-speed-serial-link-overview.md) · 다음: [03. 고속 송신기 설계](03-high-speed-transmitters.md)
