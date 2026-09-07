---
layout: page
title: "00. Final Project - 디지털 통신 시스템 시뮬레이션"
permalink: /studies/signals/digital-communications/00-final-project/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Communications/lecture_notes/00%20Final%20Project%20-%20%EB%94%94%EC%A7%80%ED%84%B8%20%ED%86%B5%EC%8B%A0%20%EC%8B%9C%EC%8A%A4%ED%85%9C%20%EC%8B%9C%EB%AE%AC%EB%A0%88%EC%9D%B4%EC%85%98.md)

{% raw %}
## 핵심 요약

이 프로젝트는 디지털 통신 시스템 전체 사슬을 MATLAB으로 구현하고 분석하는 과제이다. 주요 구성은 `bit sequence -> modulation -> channel -> receiver -> BER/복원 성능 분석`이며, QPSK 시스템의 BER 비교와 16-QAM 기반 음성 데이터 수신기/송신기 설계가 중심이다.

핵심은 다음 네 가지이다.

- AWGN 채널과 1-tap Rayleigh fading 채널에서 QPSK BER 성능을 비교한다.
- Rayleigh fading에서는 채널을 완벽히 안다고 가정하고 1-tap equalizer를 사용한다.
- `(7,4) Hamming code`를 구현하여 coded BER과 uncoded BER을 비교한다.
- 주어진 16-QAM 음성 데이터를 equalization, demodulation, error correction, dequantization으로 복원한다.

## 프로젝트 1 - QPSK BER 시뮬레이션

### 1-a. AWGN vs 1-tap Rayleigh Fading

비트 시퀀스를 QPSK로 변조한 뒤 두 채널을 통과시킨다.

- AWGN only channel
- `h ~ CN(0, 1)`인 1-tap Rayleigh fading channel

Rayleigh fading에서는 채널을 정확히 알고 있다고 가정하므로 수신 신호를 채널 계수로 나누는 1-tap equalization을 적용한다.

```text
y = h x + n
x_hat = y / h = x + n / h
```

분석할 내용은 `Eb/N0 = 0 dB ~ 10 dB`에서 BER을 구하고 두 곡선을 비교하는 것이다.

중요한 해석 포인트는 다음과 같다.

- AWGN에서는 신호 자체의 크기/위상이 채널에 의해 변하지 않고 잡음만 더해진다.
- Rayleigh fading에서는 신호가 복소 채널 계수에 의해 감쇠 및 회전된다.
- equalization은 채널 왜곡을 보상하지만, `h`가 작을 때 잡음 `n/h`가 증폭될 수 있다.
- 송신단에서 설정한 `Eb/N0`와 수신단에서 실제 경험하는 SNR은 fading 채널에서 달라질 수 있다.

## 프로젝트 1-b - Channel Coding

주어진 generator matrix `G`를 사용하여 `(7,4) Hamming code`를 구현한다.

### Hamming Code 흐름

```text
4-bit message
-> G로 encoding
-> 7-bit codeword
-> channel
-> syndrome 계산
-> 1-bit error correction
-> 4-bit message 복원
```

중요 개념:

- `(7,4)`는 4개의 정보 비트에 3개의 parity 비트를 붙인다.
- code rate는 `R = 4/7`이다.
- syndrome은 수신 codeword가 어떤 error pattern을 가졌는지 찾는 데 사용된다.
- 1-bit error는 correction 가능하지만, 여러 비트 오류는 항상 보장되지 않는다.

비교해야 할 성능:

- uncoded BER vs `Eb/N0`
- coded BER vs `Eb/N0`
- Rayleigh fading에서 coding gain이 어떻게 나타나는지

## 프로젝트 2 - Voice Data Receiver

주어진 음성 데이터는 다음 조건으로 만들어졌다고 가정한다.

| 항목 | 조건 |
|---|---|
| Constellation | 16-QAM |
| Constellation power | 1로 normalization |
| Quantization | 32 level |
| Quantization bit | 5 bit |
| Quantization 방식 | Uniform quantization |
| Sampling rate | 44.1 kHz |
| Channel coding | `(7,4) Hamming code` |
| Channel | 1-tap Rayleigh fading + noise |
| Estimated channel | `0.1938 + 0.7159i` |

### 수신기 처리 순서

```text
received symbols
-> 1-tap equalization
-> 16-QAM demodulation
-> Hamming syndrome decoding
-> 5-bit dequantization
-> audio signal reconstruction
```

### 1. Equalization

1-tap 채널이면 convolution이 단순 곱 형태가 된다.

```text
y[n] = h x[n] + w[n]
x_hat[n] = y[n] / h
```

채널 추정값이 주어졌으므로 수신 심볼을 이 값으로 나눠 constellation 회전/감쇠를 보상한다.

### 2. 16-QAM Demodulation

복원된 심볼을 16-QAM constellation point 중 가장 가까운 점으로 결정한다.

- 결정 기준: minimum distance detection
- Gray coding을 고려하여 심볼을 4-bit sequence로 변환
- constellation은 평균 power가 1이 되도록 normalization

### 3. Hamming Decoding

7-bit 단위로 codeword를 자른 뒤 syndrome을 계산한다.

```text
s = r H^T
```

syndrome이 0이면 오류 없음으로 판단한다. syndrome이 특정 열과 일치하면 그 위치에 1-bit 오류가 있다고 보고 해당 비트를 뒤집는다.

### 4. Dequantization

복원된 bitstream을 5-bit씩 묶어 0부터 31까지의 정수로 해석한다. quantization 범위가 `[-1, 1]`이므로 다음과 같이 실수 신호로 되돌린다.

```text
value = 2 * index / 31 - 1
```

복원된 배열을 sampling rate `44100 Hz`로 재생하면 음성 신호를 들을 수 있다.

## 프로젝트 2-b - 송신기 설계

복원한 음성을 다시 송신하는 송신기를 설계한다.

송신기 흐름:

```text
audio signal
-> 5-bit uniform quantization
-> (7,4) Hamming encoding
-> 16-QAM modulation
-> 1-tap Rayleigh fading channel
-> AWGN at Eb/N0 = 10 dB
```

수신은 프로젝트 2-a의 수신기를 그대로 사용한다.

중요 분석:

- 한 번 복원된 음성에는 residual noise가 남아 있다.
- 다시 채널을 통과하면 noise가 추가되고 equalization 과정에서 일부 noise가 증폭될 수 있다.
- 따라서 두 번째 복원 음성은 첫 번째 복원보다 SNR이 낮아질 가능성이 높다.

## 보너스 - 2-tap Channel 개선

2-tap 채널은 다음처럼 모델링할 수 있다.

```text
h[n] = alpha_0 delta[n] + alpha_1 delta[n-1]
```

주요 문제는 두 가지이다.

- 시간 지연이 다른 경로가 합쳐져 ISI가 발생한다.
- 각 경로의 fading 계수가 달라 amplitude/phase distortion이 생긴다.

가능한 개선 방법:

- pilot을 이용한 channel estimation
- frequency-domain equalization
- transversal equalizer로 ISI 제거
- OFDM으로 frequency selective channel을 여러 flat subchannel로 분해
- diversity 기법으로 deep fade 확률 감소

## 같이 보면 좋은 노트

- [Basic of Communications - 디지털 통신 개요](01-basic-of-communications.md)
- [Baseband Transmission - 샘플링 PCM Quantization ISI](02-baseband-transmission-pcm-quantization-isi.md)
- [Bandpass Transmission - 디지털 변조와 검파](03-bandpass-transmission.md)
- [Error Performance - Bandpass BER 성능](05-error-performance-bandpass-ber.md)
- [Channel Coding - 오류 제어 부호](06-channel-coding.md)
- [Channel Model - Multipath Fading과 Equalization](09-channel-model-multipath-fading-equalization.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **00. Final Project - 디지털 통신 시스템 시뮬레이션**를 다루며, baseband/bandpass 전송, 변조, 잡음, BER, 채널 부호, fading/equalization을 통신 시스템 관점에서 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
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
- **00. Final Project - 디지털 통신 시스템 시뮬레이션**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

다음: [00. Project Report - Rayleigh Fading 음성 통신 구현](00-project-report-rayleigh-fading.md)
