---
layout: page
title: "00. 강의계획과 DSP 전체 개요"
permalink: /studies/signals/dsp/00-course-plan-and-overview/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Signal_Processing/lecture_notes/00%20%EA%B0%95%EC%9D%98%EA%B3%84%ED%9A%8D%EA%B3%BC%20%EA%B0%9C%EC%9A%94.md)

{% raw %}
> 다음: [연구실 소개와 DSP 응용 맥락](01-lab-intro-and-dsp-applications.md)

## 강의 기본 정보

- 과목: Digital Signal Processing
- 담당: 강문기 교수, Yonsei University Superresolution Image Processing Lab.
- 교재: Oppenheim & Schafer, *Discrete-Time Signal Processing*, 3rd ed.
- 참고: J. S. Lim, *Two-Dimensional Signal and Image Processing*
- 선수과목: Signals and Systems
- 실습 환경: Matlab

## 평가 구조

| 항목 | 비중 | 메모 |
|---|---:|---|
| 중간고사 | 30% | 1.5시간 |
| 기말고사 | 50% | 2시간 |
| 과제 및 시뮬레이션 | 20% | 3-4회 simulation project, 지각 제출 불가 |

## 과목 목표

디지털 신호처리 알고리즘의 설계, 구현, 응용에서 중요한 이슈를 종합적으로 다루는 것이 목표이다. 단순히 공식 암기가 아니라 다음 흐름을 연결해서 보는 것이 중요하다.

1. 신호와 시스템을 시간/주파수 영역에서 표현한다.
2. LTI 시스템을 convolution, frequency response, z-transform으로 분석한다.
3. 필터 구조와 모델링을 행렬/선형대수 관점으로 이해한다.
4. FIR/IIR 필터와 Wiener filter를 설계한다.
5. DFT/FFT로 실제 계산 가능한 주파수 분석을 수행한다.
6. 1차원 DSP 개념을 2차원 영상 신호로 확장한다.

## 강의 전체 지도

### 1. 신호와 시스템 복습

- 이산시간 신호와 시스템
- 선형성, 시불변성, 인과성, 안정성
- LTI 시스템과 impulse response
- convolution sum
- DTFS/DTFT와 주파수 영역 표현
- 연속시간 신호의 샘플링과 재구성

핵심 연결:

$$
y[n] = x[n] * h[n]
$$

시간 영역의 convolution은 주파수 영역에서 곱셈이 된다.

$$
Y(e^{j\omega}) = X(e^{j\omega})H(e^{j\omega})
$$

### 2. 샘플링률 변환

- decimation: 샘플링률 감소
- interpolation: 샘플링률 증가
- 비정수 배율 변환: interpolation 후 decimation
- aliasing을 막기 위한 anti-aliasing low-pass filter

### 3. LTI 시스템과 선형대수

convolution을 행렬 곱으로 바꾸면 필터링, 역필터링, 복원 문제를 한 프레임으로 볼 수 있다.

$$
\mathbf{y} = H\mathbf{x}
$$

여기서 `H`는 Toeplitz 또는 circulant 구조를 갖는 시스템 행렬이 된다.

### 4. z-Transform

- z-transform 정의와 ROC
- inverse z-transform
- z-transform 성질
- LTI 시스템 함수 `H(z)`
- pole-zero plot
- 안정성/인과성과 ROC의 관계

### 5. LTI 시스템의 변환 분석

- inverse system
- `H(z)`와 frequency response `H(e^{j\omega})`
- minimum phase system
- all-pass system

### 6. 이산시간 시스템 구조와 모델링

- Direct Form I, Direct Form II
- AR, MA, ARMA 구조
- parametric signal modeling
- Yule-Walker equation

### 7. Wiener Optimal Filter

잡음이 섞인 관측 신호에서 원 신호를 최소제곱 의미로 추정한다.

$$
\hat{x}[n] = \hat{h}[n] * y[n]
$$

오차 에너지 `E{|x[n]-\hat{x}[n]|^2}`를 최소화하며, 직교성 원리를 사용한다.

### 8. Digital Filter Design

- FIR: 유한 impulse response, 정확한 linear phase 가능
- IIR: infinite impulse response, analog filter 이론 기반 설계 가능
- window method, frequency sampling, optimal equiripple
- impulse invariance, bilinear transform, Butterworth 등

### 9. DFT와 FFT

- DTFT 샘플링으로 DFT 도입
- circular shift, circular convolution, duality, symmetry
- FFT: DFT 계산량을 $O(N^2)$에서 $O(N\log N)$으로 감소
- Decimation-in-Time, Decimation-in-Frequency

### 10. 2차원 DSP

- 2D digital signal과 image
- 2D LSI system과 2D convolution
- DSFT
- 2D sampling/reconstruction
- image enhancement, restoration, coding, understanding

## 공부할 때의 큰 그림

이 강의의 중심축은 다음 한 줄로 요약할 수 있다.

$$
\text{Signal} \rightarrow \text{System} \rightarrow \text{Transform} \rightarrow \text{Design/Estimation}
$$

특히 시험이나 과제에서는 다음 대응을 빠르게 떠올려야 한다.

| 관점 | 시간/공간 영역 | 주파수/z 영역 | 선형대수 영역 |
|---|---|---|---|
| 필터링 | $y[n]=x[n]*h[n]$ | $Y=XH$ | $\mathbf{y}=H\mathbf{x}$ |
| 역문제 | $x$ 복원 | $1/H$ | $H^{-1}$ 또는 LS |
| 안정성 | impulse response 절대합 | unit circle 포함 ROC | 조건수/특이성 |
| 계산 | convolution sum | DFT/FFT | 행렬 대각화 |

## 체크포인트

- convolution, DTFT, z-transform, DFT가 서로 독립 주제가 아니라 같은 LTI 시스템을 보는 다른 좌표계임을 기억한다.
- 샘플링과 DFT는 모두 “복제/주기화”를 만든다. aliasing과 circular convolution의 원인이 된다.
- 행렬 관점은 inverse filtering, superresolution, Wiener filter로 이어진다.
- 2D DSP에서는 1D의 시간축이 이미지의 공간축 두 개로 확장된다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **00. 강의계획과 DSP 전체 개요**를 다루며, 이산시간 신호를 z-domain, frequency domain, filter structure, FFT 관점에서 분석하고 설계한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

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
- **00. 강의계획과 DSP 전체 개요**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

다음: [01. 연구실 소개와 DSP 응용 맥락](01-lab-intro-and-dsp-applications.md)
