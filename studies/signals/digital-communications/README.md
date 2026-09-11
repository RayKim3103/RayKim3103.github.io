---
layout: page
title: Digital Communications
description: >
  Digital Communications 강의 노트.
hide_description: false
sitemap: false
permalink: /studies/signals/digital-communications/
---

원본: [GitHub — Digital Communications](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Digital_Communications)

> 원본에서 같은 기말 프로젝트를 다루는 두 노트("Final Project" 스펙과 "Project Report" 결과, 둘 다 "00"번)를 하나로 병합했습니다(13개 → 12개). 각 장에 실제 숫자로 완결한 worked example(안테나 파장·raised cosine 대역폭·Q-function BER 비교·(7,4) Hamming 인코딩/오류정정·Shannon capacity·Doppler shift·AM 변조 효율·Carson's rule)을 추가해 보강했습니다.

## 강의 노트

1. [00. Final Project - Rayleigh Fading 음성 통신 시스템](00-final-project-rayleigh-fading-voice-system.md) — QPSK BER(AWGN vs Rayleigh), (7,4) Hamming coding gain 실측, 16-QAM 음성 복원(원본 병합)
2. [01. Basic of Communications - 디지털 통신 개요](01-basic-of-communications.md) — 안테나 파장 worked example
3. [02. Baseband Transmission - 샘플링 PCM Quantization ISI](02-baseband-transmission-pcm-quantization-isi.md) — raised cosine 대역폭 worked example
4. [03. Bandpass Transmission - 디지털 변조와 검파](03-bandpass-transmission.md) — coherent/noncoherent FSK tone spacing 숫자 비교
5. [04. Noise and Decision - 잡음과 최적 검출](04-noise-and-decision.md) — unipolar/bipolar BER 숫자 비교
6. [05. Error Performance - Bandpass BER 성능](05-error-performance-bandpass-ber.md) — BPSK/DPSK/BFSK BER 숫자 비교
7. [06. Channel Coding - 오류 제어 부호](06-channel-coding.md) — (7,4) Hamming G/H 행렬로 실제 인코딩·오류정정
8. [07. Channel Coding Supplement - Entropy와 Shannon Limit](07-channel-coding-supplement-entropy-shannon-limit.md) — 전화 채널 capacity worked example
9. [08. QPSK vs 16QAM - 대역폭과 BER 비교](08-qpsk-vs-16qam-ber.md) — 대역폭 수치 검산
10. [09. Channel Model - Multipath Fading과 Equalization](09-channel-model-multipath-fading-equalization.md) — Doppler shift worked example
11. [10. Amplitude Modulation - AM DSB SSB VSB](10-amplitude-modulation-am-dsb-ssb-vsb.md) — 톤 변조 효율 33% 유도
12. [11. Angle Modulation - FM PM과 SNR](11-angle-modulation-fm-pm-snr.md) — 상업 FM Carson's rule worked example
