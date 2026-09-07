---
layout: page
title: "08. QPSK vs 16QAM - 대역폭과 BER 비교"
permalink: /studies/signals/digital-communications/08-qpsk-vs-16qam-ber/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Communications/lecture_notes/08%20QPSK%20vs%2016QAM%20-%20%EB%8C%80%EC%97%AD%ED%8F%AD%EA%B3%BC%20BER%20%EB%B9%84%EA%B5%90.md)

{% raw %}
## 핵심 요약

이 자료는 QPSK와 16-QAM을 constellation, modulated signal, spectrum, BER 관점에서 비교한다. 같은 bit rate에서 16-QAM은 한 symbol에 4 bit를 싣기 때문에 QPSK보다 symbol rate가 낮고 bandwidth가 작다. 대신 constellation point가 조밀해 noise와 fading에 더 취약하여 BER 성능은 QPSK보다 나쁘다.

## 비교 조건

자료의 specification:

| 항목 | 값 |
|---|---|
| Sampling frequency | `f_s = 4.8 kHz` |
| Bit rate | `R_b = 240 bps` |
| Average bit energy | `E_b = 1` |
| Roll-off factor | `r = 0` |

비교 항목:

- constellation
- modulated signal
- spectrum
- bit error rate

## Constellation 비교

### QPSK

QPSK는 2차원 I/Q 평면에 4개의 symbol을 배치한다.

```text
M = 4
bits/symbol = log2 4 = 2
```

특징:

- symbol point 간 거리가 비교적 넓다.
- noise margin이 크다.
- BPSK와 같은 BER 성능을 가질 수 있다.

### 16-QAM

16-QAM은 I/Q 평면의 amplitude와 phase를 모두 사용해 16개의 symbol을 배치한다.

```text
M = 16
bits/symbol = log2 16 = 4
```

특징:

- 같은 symbol rate에서 QPSK보다 bit rate가 2배 높다.
- 같은 bit rate에서는 QPSK보다 symbol rate가 절반이다.
- constellation point가 더 조밀해 noise에 민감하다.

## Modulated Signal 비교

같은 bit rate를 맞추면:

- QPSK: 2 bit/symbol이므로 symbol을 더 자주 전송해야 한다.
- 16-QAM: 4 bit/symbol이므로 symbol rate가 QPSK의 절반이다.

자료에서는 QPSK가 동일 bit rate를 위해 16-QAM보다 2배 빠르게 symbol을 전송한다고 설명한다.

## Spectrum 비교

roll-off factor가 0이면 bandwidth는 symbol rate에 직접 비례한다.

동일 bit rate에서:

```text
R_s,QPSK = R_b / 2
R_s,16QAM = R_b / 4
```

따라서:

```text
B_QPSK ≈ 2 B_16QAM
```

자료의 spectrum 비교에서도 QPSK 대역폭은 약 120 Hz, 16-QAM 대역폭은 약 60 Hz로 나타난다.

## BER 비교

16-QAM의 BER 성능은 QPSK보다 떨어진다.

이유:

- QPSK는 4개 점이 상대적으로 멀리 떨어져 있다.
- 16-QAM은 같은 average bit energy에서 16개 점을 더 좁은 영역에 배치한다.
- minimum Euclidean distance가 작아져 AWGN과 fading에 더 취약하다.

비교 결과의 방향:

| 채널 | QPSK | 16-QAM |
|---|---|---|
| AWGN | 더 낮은 BER | 더 높은 BER |
| Fading | 더 낮은 BER | 더 높은 BER |

fading 환경에서는 두 방식 모두 AWGN보다 BER이 나빠지지만, 16-QAM의 취약성이 더 두드러진다.

## Trade-off 정리

| 항목 | QPSK | 16-QAM |
|---|---|---|
| bits/symbol | 2 | 4 |
| 같은 bit rate에서 bandwidth | 큼 | 작음 |
| constellation 간격 | 넓음 | 좁음 |
| BER 성능 | 좋음 | 나쁨 |
| power 효율 | 상대적으로 좋음 | 같은 BER에 더 높은 SNR 필요 |
| bandwidth 효율 | 낮음 | 높음 |

## 시험 포인트

- QPSK와 16-QAM의 bits/symbol 차이를 계산한다.
- 같은 bit rate에서 16-QAM의 bandwidth가 QPSK의 절반이 되는 이유를 설명한다.
- 16-QAM이 BER에서 불리한 이유를 constellation distance로 설명한다.
- bandwidth efficiency와 error performance의 trade-off를 정리한다.

## 같이 보면 좋은 노트

- [Bandpass Transmission - 디지털 변조와 검파](03-bandpass-transmission.md)
- [Error Performance - Bandpass BER 성능](05-error-performance-bandpass-ber.md)
- [Channel Model - Multipath Fading과 Equalization](09-channel-model-multipath-fading-equalization.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **08. QPSK vs 16QAM - 대역폭과 BER 비교**를 다루며, baseband/bandpass 전송, 변조, 잡음, BER, 채널 부호, fading/equalization을 통신 시스템 관점에서 연결한다.
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
- **08. QPSK vs 16QAM - 대역폭과 BER 비교**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [07. Channel Coding Supplement - Entropy와 Shannon Limit](07-channel-coding-supplement-entropy-shannon-limit.md) · 다음: [09. Channel Model - Multipath Fading과 Equalization](09-channel-model-multipath-fading-equalization.md)
