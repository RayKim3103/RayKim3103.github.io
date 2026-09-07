---
layout: page
title: "11. Angle Modulation - FM PM과 SNR"
permalink: /studies/signals/digital-communications/11-angle-modulation-fm-pm-snr/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Communications/lecture_notes/11%20Angle%20Modulation%20-%20FM%20PM%EA%B3%BC%20SNR.md)

{% raw %}
## 핵심 요약

이 강의는 amplitude를 일정하게 유지하고 phase 또는 instantaneous frequency에 정보를 싣는 angle modulation을 다룬다. PM과 FM의 차이, NBFM/WBFM, Bessel function 기반 FM spectrum, Carson bandwidth rule, direct/indirect FM generation, discriminator/PLL demodulation, FM SNR, threshold effect, pre-emphasis/de-emphasis가 핵심이다.

## Angle Modulated Wave

일반 형태:

```text
s(t) = A cos(theta_i(t))
theta_i(t) = omega_c t + theta(t)
```

instantaneous frequency:

```text
f_i(t) = (1 / 2pi) d theta_i(t) / dt
```

angle modulation에서는 amplitude `A`가 일정하므로 amplitude noise에 상대적으로 강할 수 있다.

## PM vs FM

### Phase Modulation

PM은 phase deviation이 message signal에 비례한다.

```text
theta(t) = k_p m(t)
```

따라서 instantaneous frequency deviation은 message의 미분에 비례한다.

```text
Delta f(t) ∝ dm(t)/dt
```

### Frequency Modulation

FM은 instantaneous frequency deviation이 message signal에 비례한다.

```text
f_i(t) = f_c + k_f m(t)
```

phase는 frequency를 적분한 값이다.

```text
theta_i(t) = 2pi f_c t + 2pi k_f integral m(t) dt
```

## NBFM

Narrowband FM은 modulation index가 작은 경우이다.

```text
beta << 1
```

NBFM은 선형 근사가 가능하다.

해석:

- AM과 bandwidth가 비슷하게 `2W` 정도이다.
- AM의 modulation term은 in-phase 성분에 붙는다.
- NBFM의 modulation term은 quadrature 성분에 붙는다.
- NBFM과 NBPM은 linear modulation처럼 다룰 수 있다.

## NBFM Phasor Representation

AM과 NBFM은 시간 파형이 비슷해 보일 수 있지만 phasor 관점에서 다르다.

| 방식 | modulation term 방향 |
|---|---|
| AM | carrier와 같은 in-phase 방향 |
| NBFM | carrier에 직교하는 quadrature 방향 |

이 차이가 envelope와 phase/frequency 변화의 물리적 의미를 결정한다.

## Wideband FM

WBFM은 modulation index `beta`가 크고 선형 근사가 불가능한 경우이다.

sinusoidal message의 FM 신호는 Bessel function으로 전개된다.

```text
s_FM(t) = A_c cos(omega_c t + beta sin omega_m t)
```

전개하면 carrier 주변에 무한히 많은 sideband가 생긴다.

sideband amplitude는 Bessel function `J_n(beta)`로 결정된다.

## FM Spectrum

FM spectrum의 특징:

- 이론적으로 sideband가 무한개 존재한다.
- 실제로는 `J_n(beta)`가 충분히 작은 항을 무시한다.
- `beta`가 커질수록 의미 있는 sideband 수가 늘어난다.

NBFM에서는 carrier와 1차 sideband 정도만 중요하지만, WBFM에서는 여러 sideband가 필요하다.

## Carson's Rule

FM bandwidth 근사:

```text
B_T ≈ 2(Delta f + W)
```

sinusoidal message에서는:

```text
B_T ≈ 2(beta + 1) f_m
```

해석:

- frequency deviation이 커지면 noise 성능은 좋아질 수 있다.
- 대신 필요한 bandwidth가 증가한다.

## Average Power in FM

angle modulation은 amplitude가 일정하므로 평균 power가 modulation index와 무관하게 일정하다.

```text
P_avg = A_c^2 / 2
```

message가 power를 sideband 사이에 재분배할 뿐 total transmitted power는 일정하다.

## Wideband FM Generation

### Indirect FM

NBFM을 먼저 만들고 frequency multiplier로 frequency deviation을 키워 WBFM을 만든다.

```text
NBFM
-> frequency multiplier
-> frequency converter
-> desired WBFM
```

특징:

- Armstrong FM transmitter 구조
- 안정적인 oscillator를 사용할 수 있다.
- multiplier 배수로 `Delta f`와 carrier frequency가 함께 증가한다.

### Direct FM

VCO를 이용하여 modulating voltage로 oscillator frequency를 직접 조절한다.

장점:

- 큰 frequency deviation을 만들기 쉽다.

단점:

- frequency stability를 보장하는 보조 장치가 필요하다.

## FM Multiplexing

여러 channel을 subcarrier에 올려 frequency multiplexing한 뒤 RF 대역으로 올릴 수 있다.

상업 FM stereo 방송에서는:

- `L+R` audio channel
- `L-R` stereo difference channel
- stereo pilot
- SCA channel

등이 baseband spectrum 안에 배치된다.

## FM Demodulation

### Frequency Discriminator

FM은 frequency 변화가 message이므로, frequency 변화를 amplitude 변화로 바꾼 뒤 envelope detection할 수 있다.

아이디어:

```text
FM signal
-> differentiator 또는 slope discriminator
-> envelope detector
-> message
```

실제 구현:

- balanced frequency discriminator
- Foster-Seeley discriminator
- ratio detector
- time-delay demodulator

### PLL Demodulation

PLL은 phase comparator, loop filter, VCO로 구성된다.

PLL이 lock 상태이면 VCO 제어 전압이 입력 FM 신호의 instantaneous frequency 변화에 비례하므로 demodulated message가 된다.

## FM Receiver

일반 FM broadcast receiver는 superheterodyne 구조를 사용한다.

```text
RF amplifier
-> mixer
-> IF amplifier
-> limiter
-> discriminator
-> audio amplifier
```

commercial FM:

- RF band: 88 MHz ~ 108 MHz
- IF: 10.7 MHz
- channel bandwidth: 약 200 kHz
- peak frequency deviation: 75 kHz

## SNR in FM Reception

FM 수신기는 limiter를 사용하여 amplitude noise를 줄이고, discriminator로 frequency variation을 복조한다.

high carrier-to-noise condition에서는 FM output SNR이 AM보다 좋아질 수 있다.

중요한 trade-off:

- frequency deviation을 크게 하면 output SNR 개선
- 필요한 bandwidth 증가

NBFM은 bandwidth가 작지만 WBFM만큼의 SNR 개선을 얻지 못한다.

## Threshold Effect

FM은 input CNR이 충분히 클 때 noise quieting 효과를 보인다. 하지만 input SNR이 낮아지면 output SNR이 갑자기 급격히 나빠진다. 이것이 threshold effect이다.

원인:

- noise phasor가 signal phasor와 비슷하거나 더 커지면 resultant phasor가 원점을 감쌀 수 있다.
- 이때 phase가 급격히 뛰면서 impulse-like spike noise가 생긴다.

해석:

- high SNR: FM이 AM보다 유리
- low SNR: threshold 아래에서는 FM 이득이 사라지거나 크게 줄어듦

## Pre-emphasis and De-emphasis

FM demodulator output noise는 high-frequency 영역에서 더 커지는 경향이 있다.

개선 방법:

```text
transmitter: high-frequency message 성분 강조 (pre-emphasis)
receiver: 원래 spectrum으로 되돌림 (de-emphasis)
```

효과:

- 수신 후 high-frequency noise를 줄인다.
- de-emphasis 후 noise spectrum을 더 white하게 만든다.

## CW Modulation 비교

| 방식 | 대표 응용 | 특징 |
|---|---|---|
| DSB-SC | analog instrumentation, multiplexing | 동기 검파 필요 |
| DSB-LC | AM radio, voice | envelope detection 가능 |
| SSB-SC | point-to-point voice, multiplexing | bandwidth 절약 |
| SSB-LC | point-to-point voice | 일부 carrier 포함 |
| VSB | TV video, facsimile | SSB/DSB 절충 |
| FM | broadcast radio, mobile radio | noise 성능 좋음, bandwidth 큼 |
| PM | telemetry, digital data | phase 정보 이용 |

## 시험 포인트

- PM은 phase가 message에 비례하고, FM은 instantaneous frequency가 message에 비례한다.
- NBFM은 linear approximation이 가능하고 bandwidth가 AM과 비슷하다.
- WBFM spectrum은 Bessel function sideband로 표현된다.
- Carson's rule `B_T ≈ 2(Delta f + W)`를 기억한다.
- FM은 amplitude 일정, total power 일정이다.
- discriminator와 PLL의 FM demodulation 원리를 비교한다.
- threshold effect와 pre/de-emphasis의 목적을 설명할 수 있어야 한다.

## 같이 보면 좋은 노트

- [Amplitude Modulation - AM DSB SSB VSB](10-amplitude-modulation-am-dsb-ssb-vsb.md)
- [Noise and Decision - 잡음과 최적 검출](04-noise-and-decision.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **11. Angle Modulation - FM PM과 SNR**를 다루며, baseband/bandpass 전송, 변조, 잡음, BER, 채널 부호, fading/equalization을 통신 시스템 관점에서 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 통신 주제에서는 constellation 거리, noise variance, receiver decision rule이 BER 식으로 이어진다.
- 채널 모델이 AWGN인지 fading인지에 따라 diversity와 equalization 필요성이 달라진다.
- 통신 시스템은 waveform 설계, channel distortion, noise, receiver decision이 하나의 확률적 chain으로 이어진다.
- BER 식은 constellation geometry와 noise projection에서 유도되는 거리 문제로 이해한다.
- channel coding은 redundancy를 추가해 reliability를 높이지만 rate, complexity, latency 비용을 만든다.

### 문제 풀이 또는 구현 루틴

- 문제를 source, modulation, channel, receiver, decision metric 순서로 분해한다.
- SNR, Eb/N0, Es/N0의 정의와 단위를 먼저 맞춘 뒤 BER/SER 공식을 적용한다.
- fading 문제는 channel coefficient, diversity, equalizer가 신호공간을 어떻게 바꾸는지 그림으로 본다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- dB와 linear scale을 섞어 계산하면 결과가 크게 틀어진다.
- QPSK와 16QAM의 spectral efficiency와 noise robustness trade-off를 구분해야 한다.
- ISI는 단순 잡음이 아니라 symbol 간 구조적 간섭이므로 filter/equalizer 관점이 필요하다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- receiver가 실제로 비교하는 decision variable은 무엇인가?
- 이 변조 방식에서 최소 Euclidean distance는 얼마인가?
- 채널 부호나 equalizer가 rate, latency, complexity에 어떤 비용을 더하는가?
- **11. Angle Modulation - FM PM과 SNR**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [10. Amplitude Modulation - AM DSB SSB VSB](10-amplitude-modulation-am-dsb-ssb-vsb.md)
