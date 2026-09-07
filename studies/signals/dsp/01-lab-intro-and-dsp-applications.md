---
layout: page
title: "01. 연구실 소개와 DSP 응용 맥락"
permalink: /studies/signals/dsp/01-lab-intro-and-dsp-applications/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Signal_Processing/lecture_notes/01%20%EC%97%B0%EA%B5%AC%EC%8B%A4%20%EC%86%8C%EA%B0%9C%EC%99%80%20DSP%20%EC%9D%91%EC%9A%A9%20%EB%A7%A5%EB%9D%BD.md)

{% raw %}
﻿---
title: "01. 연구실 소개와 DSP 응용 맥락"
pages: 3
tags: [DSP, lecture-note, applications, image-processing]
---

# 01. 연구실 소개와 DSP 응용 맥락

> 이전: [강의계획과 DSP 전체 개요](00-course-plan-and-overview.md)
> 다음: [DSP Introduction](02-dsp-introduction.md)

## 자료의 위치

이 PDF는 본격적인 수식 강의라기보다, Yonsei University SuperResolution Image Processing Lab의 연구 분야를 통해 DSP가 실제로 어디에 쓰이는지 보여주는 소개 자료이다. 텍스트 추출상 한글 인코딩이 많이 깨져 있지만, 자료의 중심 주제는 초해상도 영상처리, 복원, 의료영상, 원격탐사, 특수 센서 영상, 자율주행용 영상 인식 등이다.

## 연구실 핵심 키워드

- Superresolution image processing
- Digital image restoration
- Low-resolution/low-SNR image enhancement
- Multi-sensor image reconstruction
- Medical imaging
- Remote sensing
- Camera ISP
- ToF, light-field camera, LWIR 등 특수 목적 센서

## DSP 관점에서 보는 연구 주제

### 초해상도 영상처리

초해상도는 낮은 해상도의 관측 영상에서 더 높은 해상도의 영상을 복원하는 문제이다. DSP 관점에서는 다음 모델로 이해할 수 있다.

$$
\mathbf{y}_k = D_k B_k M_k \mathbf{x} + \mathbf{n}_k
$$

- $\mathbf{x}$: 복원하고 싶은 고해상도 영상
- $\mathbf{y}_k$: $k$번째 저해상도 관측 영상
- $M_k$: motion/warping
- $B_k$: blur
- $D_k$: down-sampling
- $\mathbf{n}_k$: noise

이 모델은 뒤의 [LTI System to Linear Algebra](05-lti-system-to-linear-algebra.md)와 직접 연결된다. 영상처리 문제를 행렬 방정식으로 쓰면 복원은 inverse problem이 된다.

### 영상 복원

흐림, 잡음, 센서 한계로 손상된 영상을 복구하는 문제이다.

대표적인 관측 모델:

$$
y[m,n] = h[m,n] * x[m,n] + v[m,n]
$$

여기서 $h[m,n]$은 blur point-spread function, $v[m,n]$은 noise이다. 복원은 단순히 $1/H$를 곱하는 문제가 아니라, 잡음 증폭과 안정성 문제를 함께 다뤄야 한다. 이 때문에 [Wiener Optimal Filter](10-wiener-optimal-filter.md)가 중요해진다.

### 의료영상

Digital angiography, sonography, MRI/CT 같은 의료영상 장비는 대부분 DSP 기반이다.

- Fourier Slice Theorem 기반 재구성
- 저선량 영상의 noise reduction
- 저해상도/저 SNR 환경에서 영상 품질 향상
- 다중 센서 또는 다중 프레임을 이용한 복원

의료영상에서는 원본 신호를 직접 볼 수 없고 관측된 투영/샘플/잡음 신호로부터 내부 구조를 추정한다. 따라서 sampling, inverse problem, regularization이 모두 중요하다.

### 원격탐사와 특수 센서

Remote sensing, multi-spectral/hyperspectral imaging, ToF, LWIR 등의 센서는 일반 RGB 카메라와 다른 물리적 특성을 갖는다.

- 공간 해상도와 스펙트럼 해상도의 trade-off
- 센서 잡음과 결측 데이터 보정
- 다중 센서 fusion
- 저조도/극저조도 환경의 영상 복원

DSP 관점에서는 서로 다른 sampling grid, point-spread function, spectral response를 하나의 관측 모델로 통합하는 문제가 된다.

## 이 자료가 강의 전체와 연결되는 방식

| 응용 문제 | 강의 개념 |
|---|---|
| 초해상도 복원 | LTI/LSI system, inverse filtering, least squares |
| 잡음 제거 | Wiener filter, power spectrum, correlation |
| 영상 압축 | DFT/FFT, frequency-domain representation |
| 센서 영상 처리 | sampling, aliasing, reconstruction |
| 2D 영상 분석 | 2D convolution, DSFT, 2D sampling |

## 핵심 정리

- DSP는 오디오, 통신, 영상, 의료, 센서 등 거의 모든 공학 시스템의 기반 도구이다.
- 영상처리에서는 “실제 장면”과 “센서 관측” 사이에 blur, sampling, noise가 개입한다.
- 많은 복원 문제는 $\mathbf{y}=H\mathbf{x}+\mathbf{n}$ 형태의 inverse problem으로 정식화된다.
- 이 강의의 수학적 도구는 연구실의 초해상도/복원 문제를 이해하기 위한 언어이기도 하다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **01. 연구실 소개와 DSP 응용 맥락**를 다루며, 이산시간 신호를 z-domain, frequency domain, filter structure, FFT 관점에서 분석하고 설계한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- 시간영역 convolution, 주파수영역 곱셈, z-domain pole-zero는 같은 LTI 시스템을 보는 세 가지 창이다.
- filter 설계에서는 magnitude response뿐 아니라 phase, stability, structure, coefficient quantization도 중요하다.
- sampling rate change와 FFT는 aliasing, circular convolution, zero padding 개념을 정확히 구분해야 한다.

### 문제 풀이 또는 구현 루틴

- impulse response, difference equation, transfer function, frequency response를 서로 변환한다.
- pole-zero plot으로 stability와 대략적인 frequency response를 먼저 예측한다.
- DFT/FFT 계산에서는 주기적 해석인지 linear convolution 해석인지 길이 조건을 확인한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- DFT의 circular convolution을 linear convolution과 혼동하면 wrap-around aliasing이 생긴다.
- z-transform의 ROC를 빼면 causality와 stability 판단이 불완전하다.
- sampling rate를 바꿀 때 anti-aliasing/anti-imaging filter를 놓치기 쉽다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 시스템은 causal, stable, LTI 조건을 만족하는가?
- pole과 zero가 magnitude response의 어느 부분을 키우거나 줄이는가?
- finite-length 계산에서 boundary와 padding을 어떻게 처리했는가?
- **01. 연구실 소개와 DSP 응용 맥락**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [00. 강의계획과 DSP 전체 개요](00-course-plan-and-overview.md) · 다음: [02. DSP Introduction](02-dsp-introduction.md)
