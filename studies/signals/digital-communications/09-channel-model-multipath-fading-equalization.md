---
layout: page
title: "09. Channel Model - Multipath Fading과 Equalization"
permalink: /studies/signals/digital-communications/09-channel-model-multipath-fading-equalization/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Communications/lecture_notes/09%20Channel%20Model%20-%20Multipath%20Fading%EA%B3%BC%20Equalization.md)

{% raw %}
## 핵심 요약

이 강의는 무선 채널에서 발생하는 multipath 현상과 fading channel model을 설명한다. 전파는 반사, 산란, 회절을 거쳐 여러 경로로 수신되며, 이로 인해 시간, 공간, 주파수 영역에서 퍼짐이 발생한다. 채널 특성은 large/small scale fading, flat/frequency-selective fading, fast/slow fading으로 분류된다. 마지막으로 pilot 기반 channel estimation과 equalization 개념을 다룬다.

## Multipath Phenomena

송신 안테나에서 나간 신호는 직접 경로뿐 아니라 주변 구조물에 의한 반사/산란 경로를 통해 수신 안테나에 도달한다.

multipath가 만드는 왜곡:

1. 시간 영역 퍼짐
2. 공간 영역 퍼짐
3. 주파수 영역 퍼짐

## Time-Spreading

각 경로는 서로 다른 길이와 반사/산란 특성을 갖는다.

따라서:

- 수신 신호 전력이 경로마다 달라진다.
- 도착 시간이 달라져 delay spread가 생긴다.
- 서로 다른 위상으로 합쳐져 constructive/destructive interference가 발생한다.

시간 지연이 symbol duration에 비해 크면 ISI가 발생할 수 있다.

## Space-Spreading

다중경로 신호는 서로 다른 입사각으로 수신 안테나에 도달한다.

공간 영역에서는 수신 신호 전력이 angle of arrival에 따라 분포한다. 이는 antenna array, beamforming, diversity 설계와 연결된다.

## Frequency-Spreading

송수신기 또는 산란체가 움직이면 Doppler shift가 발생한다.

Doppler shift는 이동 방향과 입사 방향 사이의 각도에 따라 달라진다.

```text
f_D ∝ v cos(theta) / lambda
```

입사각이 여러 방향에 분포하면 주파수 영역에서도 신호가 퍼진다.

## Fading 규모 분류

### Long-Term Fading

large-scale fading 또는 log-normal fading이라고도 한다.

원인:

- 거리 증가에 따른 path loss
- 큰 건물/지형에 의한 shadowing

특징:

- 긴 거리/시간 scale에서 천천히 변화
- log scale에서 normal 분포처럼 보이는 경향

### Short-Term Fading

small-scale fading이라고도 한다.

원인:

- 수신기 주변 산란체의 상대 위치 변화
- 다중경로 위상 합성

특징:

- 짧은 거리 이동에도 빠르게 수신 전력이 변함
- 직접 경로 유무에 따라 Rayleigh/Rician으로 모델링

## Rayleigh vs Rician Fading

| 모델 | 조건 | envelope 분포 |
|---|---|---|
| Rayleigh | LOS 없음 | Rayleigh distribution |
| Rician | LOS 있음 | Rician distribution |

Rayleigh fading:

- 직접 경로가 없는 NLOS 상황
- 많은 산란 성분의 복소합이 zero-mean complex Gaussian
- phase는 `[0, 2pi)`에서 uniform

Rician fading:

- 직접 경로 LOS 성분 존재
- deterministic dominant component + scattered Gaussian components
- noncentrality parameter가 존재

## Flat Fading vs Frequency Selective Fading

### Frequency Non-selective 또는 Flat Fading

조건:

- signal bandwidth < coherence bandwidth
- symbol duration > channel impulse response length

수신 신호:

```text
y(t) ≈ h(t) x(t)
```

전체 주파수 대역이 거의 같은 channel gain을 겪는다.

### Frequency Selective Fading

조건:

- signal bandwidth > coherence bandwidth
- symbol duration < channel impulse response length

수신 신호:

```text
y(t) = x(t) * h(t)
```

주파수별 channel response가 달라지고 ISI가 생길 수 있다.

## Fast Fading vs Slow Fading

### Fast Fading

조건:

- channel 변화가 symbol duration보다 빠름
- symbol duration > coherence time
- signal bandwidth < maximum Doppler shift

한 symbol 안에서도 채널이 변할 수 있어 검출이 어려워진다.

### Slow Fading

조건:

- channel 변화가 symbol duration보다 느림
- symbol duration < coherence time

여러 symbol 동안 같은 channel coefficient를 겪는 것으로 근사할 수 있다.

## Multipath Channel Model

Jakes' model 계열의 multiray model은 채널 impulse response를 다음처럼 표현한다.

```text
c(tau; t) = sum_i alpha_i(t) delta(tau - tau_i(t))
```

여기서:

- `alpha_i(t)`: i번째 multipath의 복소 path gain
- `tau_i(t)`: i번째 multipath delay
- `t`: 채널의 시변성을 나타내는 시간

많은 ray가 비슷한 amplitude와 delay를 가지면서 서로 다른 phase로 합쳐지면, path gain은 complex Gaussian으로 근사되고 envelope는 Rayleigh 분포가 된다.

## Statistical Channel Model

Fading channel은 통계적 모델로 표현한다.

대표 모델:

- Rayleigh distribution
- Rician distribution
- Nakagami-m distribution

이 강의에서는 Rayleigh와 Rician이 중심이다.

## Channel Estimation

실제 수신 신호는 채널 때문에 원래 송신 신호와 달라진다. 이를 보상하려면 먼저 채널을 추정해야 한다.

일반 방식:

```text
known pilot signal 전송
-> 수신된 pilot 관찰
-> channel response 추정
```

frequency domain에서:

```text
Y(f) = H(f) X(f) + N(f)
```

pilot `X(f)`를 알고 있으면 `H(f)`를 추정할 수 있다.

## Channel Equalization

Equalization은 추정한 채널을 이용해 왜곡된 신호를 원래 형태에 가깝게 되돌리는 과정이다.

가장 단순한 1-tap equalization:

```text
X_hat(f) = Y(f) / H_hat(f)
```

주의:

- flat fading에서는 효과적이다.
- frequency selective fading에서는 1-tap equalization이 충분하지 않을 수 있다.
- `H_hat(f)`가 작으면 noise enhancement가 발생한다.

## 16-QAM/64-QAM Equalization 예

자료는 16-QAM, 64-QAM constellation에서 equalization 전후를 비교한다.

해석:

- channel distortion은 constellation point를 회전/확산시킨다.
- equalization은 point cloud를 원래 위치로 되돌린다.
- 64-QAM은 constellation 간격이 좁아 16-QAM보다 채널 추정 오차와 noise에 더 민감하다.

## 시험 포인트

- multipath가 시간/공간/주파수 퍼짐을 만든다는 점을 설명한다.
- large-scale fading과 small-scale fading을 구분한다.
- Rayleigh와 Rician의 LOS 조건 차이를 기억한다.
- flat fading과 frequency selective fading의 조건을 bandwidth/coherence bandwidth로 설명한다.
- fast/slow fading을 coherence time과 symbol duration으로 판단한다.
- channel estimation은 pilot 기반, equalization은 추정 채널 보상이다.

## 같이 보면 좋은 노트

- [Final Project - 디지털 통신 시스템 시뮬레이션](00-final-project.md)
- [Project Report - Rayleigh Fading 음성 통신 구현](00-project-report-rayleigh-fading.md)
- [Error Performance - Bandpass BER 성능](05-error-performance-bandpass-ber.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **09. Channel Model - Multipath Fading과 Equalization**를 다루며, baseband/bandpass 전송, 변조, 잡음, BER, 채널 부호, fading/equalization을 통신 시스템 관점에서 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 신호 주제에서는 시간영역 연산이 주파수/z-domain에서 어떻게 단순해지는지 변환쌍으로 연결한다.
- sampling과 DFT에서는 주기화, aliasing, zero padding을 분리해 생각해야 한다.
- 통신 주제에서는 constellation 거리, noise variance, receiver decision rule이 BER 식으로 이어진다.
- 채널 모델이 AWGN인지 fading인지에 따라 diversity와 equalization 필요성이 달라진다.
- 통신 시스템은 waveform 설계, channel distortion, noise, receiver decision이 하나의 확률적 chain으로 이어진다.

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
- **09. Channel Model - Multipath Fading과 Equalization**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [08. QPSK vs 16QAM - 대역폭과 BER 비교](08-qpsk-vs-16qam-ber.md) · 다음: [10. Amplitude Modulation - AM DSB SSB VSB](10-amplitude-modulation-am-dsb-ssb-vsb.md)
