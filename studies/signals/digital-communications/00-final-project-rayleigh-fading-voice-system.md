---
layout: page
title: "00. Final Project - Rayleigh Fading 음성 통신 시스템"
permalink: /studies/signals/digital-communications/00-final-project-rayleigh-fading-voice-system/
sitemap: false
---

- **원본 노트**: [GitHub — Final Project (스펙)](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Communications/lecture_notes/00%20Final%20Project%20-%20%EB%94%94%EC%A7%80%ED%84%B8%20%ED%86%B5%EC%8B%A0%20%EC%8B%9C%EC%8A%A4%ED%85%9C%20%EC%8B%9C%EB%AE%AC%EB%A0%88%EC%9D%B4%EC%85%98.md) · [Project Report(실측 결과)](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Communications/lecture_notes/00%20Project%20Report%20-%20Rayleigh%20Fading%20%EC%9D%8C%EC%84%B1%20%ED%86%B5%EC%8B%A0%20%EA%B5%AC%ED%98%84.md)

{% raw %}
> 원본은 같은 프로젝트를 다루는 두 노트(과제 스펙 "Final Project"와 학생 결과 보고서 "Project Report")로 나뉘어 있었고 둘 다 "00"번을 달고 있었습니다. 스펙(무엇을 구현해야 하는가)과 실측 결과(실제 BER 표, 복원 정확도)를 이 한 파일로 합쳐 정리했습니다.

## 핵심 요약

이 프로젝트는 디지털 통신 시스템 전체 사슬(bit → modulation → channel → receiver → BER/복원 성능)을 MATLAB으로 구현하고 분석하는 과제이다. 핵심은 네 가지다.

- AWGN 채널과 1-tap Rayleigh fading 채널에서 QPSK BER 성능을 비교한다.
- Rayleigh fading에서는 채널을 완벽히 안다고 가정하고 1-tap equalizer를 사용한다.
- $$(7,4)$$ Hamming code를 구현하여 coded BER과 uncoded BER을 비교한다.
- 주어진 16-QAM 음성 데이터를 equalization, demodulation, error correction, dequantization으로 복원한다(실제로 복원한 곡은 BLACKPINK "Shut Down").

## 배경 — Rayleigh Fading Channel

### Large-scale vs Small-scale Fading

| 분류 | 원인 | 특징 |
|---|---|---|
| Large-scale (long-term) | path loss, shadowing | 거리 증가·큰 장애물에 의한 장기적 감쇠, log-normal 근사 |
| Small-scale (short-term) | 다중경로 반사·산란체 위치 변화 | 짧은 거리에서도 빠르게 변하는 수신 전력 |

| Small-scale 모델 | 조건 | envelope 분포 |
|---|---|---|
| Rician | LOS 있음 | LOS 성분 + 산란 성분 (Rician) |
| Rayleigh | LOS 없음 | 산란 경로 합 → zero-mean complex Gaussian → envelope Rayleigh |

다중경로 채널의 일반형:
$$
c(\tau;t)=\sum_i \alpha_i(t)\,\delta(\tau-\tau_i(t))
$$
1-tap Rayleigh fading으로 단순화하면 $$h[n]=\alpha_0\delta[n]$$, $$y[n]=hx[n]+w[n]$$, $$h\sim\mathcal{CN}(0,1)$$.

## 프로젝트 1 — QPSK BER 시뮬레이션

### 1-a. AWGN vs 1-tap Rayleigh Fading

$$
\text{AWGN}:\ y=x+n \qquad\qquad \text{Rayleigh}:\ y=hx+n,\quad \hat{x}=y/h=x+n/h
$$

$$E_b/N_0=0\sim10\text{ dB}$$에서 BER을 구해 비교한다.

| 해석 포인트 |
|---|
| AWGN은 신호 크기·위상이 안 변하고 잡음만 더해짐 |
| Rayleigh는 신호가 복소 채널 계수로 감쇠·회전됨 |
| equalization은 왜곡을 보상하지만 $$h$$가 작을 때 $$n/h$$가 잡음을 증폭시킴 |
| 송신단 설정 $$E_b/N_0$$와 수신단 실제 instantaneous SNR이 fading에서는 다름 |

### 1-b. Channel Coding — $$(7,4)$$ Hamming Code

$$
\text{4-bit message}\xrightarrow{G}\text{7-bit codeword}\to\text{channel}\to\text{syndrome}\to\text{1-bit correction}\to\text{4-bit 복원}
$$

code rate $$R=4/7$$. syndrome으로 error pattern을 찾고, 1-bit error는 correction 가능하지만 2-bit 이상은 보장되지 않는다.

### 실측 BER 결과 (Project Report)

**Rayleigh fading 채널**

| $$E_b/N_0$$ | Coded BER | Uncoded BER |
|---:|---:|---:|
| 1 dB | 0.1034 | 0.1267 |
| 4 dB | 0.0498 | 0.0774 |
| 7 dB | 0.0214 | 0.0434 |
| 10 dB | 0.0086 | 0.0235 |

**AWGN 채널**

| $$E_b/N_0$$ | Coded BER | Uncoded BER |
|---:|---:|---:|
| 1 dB | 0.02494 | 0.0561 |
| 4 dB | 0.00138 | 0.0124 |
| 7 dB | 약 $$7\times10^{-6}$$ | 0.0067 |
| 10 dB | 매우 작음 | 약 $$5\times10^{-6}$$ |

**해석**: coded BER이 uncoded보다 전 구간에서 낮다 — coding은 bandwidth를 더 쓰는 대신 error correction 능력을 얻는다. 같은 BER을 얻는 데 필요한 $$E_b/N_0$$가 줄어드는 효과가 **coding gain**이다. 예를 들어 AWGN에서 uncoded BER $$\approx0.0124$$(4dB)는 coded 곡선에서 4dB보다 훨씬 낮은 $$E_b/N_0$$로도 달성되므로, 이 코드는 저 SNR 구간에서 뚜렷한 양의 coding gain을 보인다. Rayleigh 채널은 AWGN보다 전 구간 BER이 한 자릿수 이상 높다 — **단순 power 증가보다 coding/equalization의 조합이 fading에서 훨씬 중요**하다는 결론.

## 프로젝트 2 — Voice Data Receiver

주어진 음성 데이터 스펙:

| 항목 | 값 |
|---|---|
| Constellation | 16-QAM (평균 power 1로 정규화) |
| Quantization | 32 level, 5 bit, uniform |
| Sampling rate | 44.1 kHz |
| Channel coding | $$(7,4)$$ Hamming |
| Channel | 1-tap Rayleigh fading + noise |
| 추정 채널 | $$\hat h = 0.1938+0.7159i$$ |

### 수신기 처리 순서

$$
\text{received symbols}\to\text{1-tap equalization}\to\text{16-QAM demod}\to\text{Hamming decoding}\to\text{5-bit dequant.}\to\text{audio}
$$

**1. Equalization**: $$\hat x[n]=y[n]/\hat h$$ (1-tap이라 convolution이 곱셈이 됨).

**2. 16-QAM demodulation**: minimum-distance detection으로 가장 가까운 constellation point 선택, Gray coding으로 4-bit sequence 복원.

**3. Hamming decoding**: $$s=rH^T$$. $$s=0$$이면 오류 없음, $$s$$가 $$H^T$$의 특정 열과 일치하면 그 위치의 1-bit를 뒤집는다.

**4. Dequantization**: 5-bit → 0~31 정수 → $$[-1,1]$$ 복원:
$$
\text{value} = \frac{2\cdot\text{index}}{31}-1
$$
44100 Hz로 재생하면 음성 신호 복원.

## 프로젝트 2-b — 복원 음성 재송신

복원한 음성을 다시 송신하는 송신기: $$\text{audio}\to\text{5-bit quant.}\to(7,4)\text{ Hamming}\to\text{16-QAM Gray}\to\text{1-tap Rayleigh}\to\text{AWGN}(E_b/N_0{=}10\text{dB})$$. 수신은 프로젝트 2-a의 수신기를 재사용한다.

**분석**: 재수신 결과는 첫 복원보다 잡음이 더 크다 — (1) 첫 복원 신호에 residual noise가 이미 남아있고, (2) 새 AWGN이 추가되며, (3) equalization의 $$1/\hat h$$가 noise도 함께 증폭시키기 때문. 따라서 두 번째 복원 음성의 SNR은 첫 번째보다 낮아질 가능성이 높다.

## 보너스 — 2-tap Channel 개선

$$
h[n] = \alpha_0\delta[n] + \alpha_1\delta[n-1]
$$

두 문제: (1) 지연이 다른 경로가 합쳐져 ISI 발생 (2) 각 경로 fading 계수가 달라 amplitude/phase distortion.

개선 방향: 주파수 영역에서 $$H(e^{j\omega})=\alpha_0+\alpha_1 e^{-j\omega}$$를 추정 → $$1/H(e^{j\omega})$$ equalizer 설계, transversal equalizer로 ISI 제거, pilot 기반 channel estimation, OFDM(frequency selective 채널을 여러 flat subchannel로 분해), diversity로 deep fade 확률 감소.

## 핵심 정리

- AWGN과 Rayleigh fading은 BER 곡선의 감소 속도가 근본적으로 다르다(fading이 훨씬 완만).
- $$(7,4)$$ Hamming coding gain은 SNR이 낮을수록, fading 채널일수록 더 두드러진다.
- 1-tap equalization + minimum-distance demod + syndrome decoding + dequantization을 결합하면 손상된 음성을 복원할 수 있다.
- equalization의 $$1/\hat h$$ noise enhancement는 재송신 시 성능 저하로 누적된다.
- 2-tap 이상의 채널은 주파수 선택적 페이딩·ISI를 유발하며, OFDM/transversal equalizer/diversity로 대응한다.

## 복습 질문

- 1-tap Rayleigh equalization $$\hat x=y/h=x+n/h$$에서 $$h$$가 작을 때 무슨 문제가 생기는가?
- 실측 표에서 coded vs uncoded BER 차이가 AWGN과 Rayleigh 중 어느 쪽에서 더 뚜렷한가, 그 이유는?
- Hamming syndrome $$s=rH^T$$로 1-bit error를 correction하는 절차를 설명할 수 있는가?
- 복원한 음성을 재송신하면 왜 SNR이 더 나빠지는가? (residual noise, 추가 AWGN, noise enhancement 세 가지)
- 2-tap 채널이 1-tap과 달리 ISI를 유발하는 이유와, 그 대응책(OFDM 등)을 설명할 수 있는가?

## 같이 보면 좋은 노트

- [Basic of Communications - 디지털 통신 개요](01-basic-of-communications.md)
- [Baseband Transmission - 샘플링 PCM Quantization ISI](02-baseband-transmission-pcm-quantization-isi.md)
- [Bandpass Transmission - 디지털 변조와 검파](03-bandpass-transmission.md)
- [Error Performance - Bandpass BER 성능](05-error-performance-bandpass-ber.md)
- [Channel Coding - 오류 제어 부호](06-channel-coding.md)
- [Channel Model - Multipath Fading과 Equalization](09-channel-model-multipath-fading-equalization.md)

{% endraw %}

---

다음: [01. Basic of Communications - 디지털 통신 개요](01-basic-of-communications.md)
