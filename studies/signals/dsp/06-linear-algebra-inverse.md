---
layout: page
title: "06. Linear Algebra Inverse"
permalink: /studies/signals/dsp/06-linear-algebra-inverse/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Signal_Processing/lecture_notes/06%20Linear%20Algebra%20Inverse.md)

{% raw %}
﻿---
title: "06. Linear Algebra Inverse"
pages: 5
tags: [DSP, lecture-note, inverse-problem, least-squares]
---

# 06. Linear Algebra Inverse

> 이전: [LTI System to Linear Algebra](05-lti-system-to-linear-algebra.md)
> 다음: [Z-Transform Introduction](07-z-transform-introduction.md)

## 학습 목표

이 자료는 DSP 문제를 행렬 방정식으로 보았을 때 inverse가 언제 가능한지, 불가능할 때 least squares inverse를 어떻게 쓰는지 정리한다.

## 기본 모델

많은 DSP 문제는 다음 형태로 쓸 수 있다.

$$
\mathbf{y}=H\mathbf{x}
$$

- $\mathbf{x}$: 입력 또는 원 신호
- $H$: 시스템, 필터, 관측 행렬
- $\mathbf{y}$: 출력 또는 관측 신호

잡음이 있으면:

$$
\mathbf{y}=H\mathbf{x}+\mathbf{n}
$$

## Direct Inverse

$H$가 정방행렬이고 nonsingular이면

$$
\mathbf{x}=H^{-1}\mathbf{y}
$$

로 복원할 수 있다.

하지만 실제 DSP에서는 direct inverse가 잘 되지 않는 경우가 많다.

1. $H$가 정방행렬이 아닐 수 있다.
2. $H$가 singular일 수 있다.
3. $H$가 거의 singular라 noise가 크게 증폭될 수 있다.
4. 관측 $\mathbf{y}$가 noise를 포함한다.

## Overdetermined Case

방정식 수가 미지수 수보다 많은 경우:

$$
H \in \mathbb{R}^{m\times n}, \quad m>n
$$

일반적으로 모든 방정식을 정확히 만족하는 $\mathbf{x}$가 없을 수 있다. 이때 residual을 최소화한다.

$$
\hat{\mathbf{x}} = \arg\min_{\mathbf{x}}\|\mathbf{y}-H\mathbf{x}\|_2^2
$$

정규방정식:

$$
H^TH\hat{\mathbf{x}}=H^T\mathbf{y}
$$

해:

$$
\hat{\mathbf{x}}=(H^TH)^{-1}H^T\mathbf{y}
$$

복소수 행렬에서는 transpose 대신 Hermitian transpose를 쓴다.

$$
\hat{\mathbf{x}}=(H^HH)^{-1}H^H\mathbf{y}
$$

## Underdetermined Case

미지수 수가 방정식 수보다 많은 경우:

$$
m<n
$$

해가 무수히 많거나 추가 정보 없이는 결정되지 않는다. 이때 최소 norm 해, regularization, prior information이 필요하다.

대표적 정식화:

$$
\hat{\mathbf{x}}=
\arg\min_{\mathbf{x}}\|H\mathbf{x}-\mathbf{y}\|_2^2
+\lambda R(\mathbf{x})
$$

여기서 $R(\mathbf{x})$는 smoothness, sparsity 같은 prior를 반영한다.

## Ill-Posed/Singular Case

$H$가 singular 또는 ill-conditioned이면 작은 noise가 복원 결과에서 크게 증폭된다.

예:

$$
Y(e^{j\omega})=H(e^{j\omega})X(e^{j\omega})+N(e^{j\omega})
$$

단순 inverse:

$$
\hat{X}(e^{j\omega})=\frac{Y(e^{j\omega})}{H(e^{j\omega})}
$$

만약 $|H(e^{j\omega})|$가 매우 작으면 noise term도 크게 증폭된다.

## DSP 문제 유형

| 주어진 것 | 구할 것 | 문제 이름 |
|---|---|---|
| $x$, $T\{\cdot\}$ | $y$ | filtering |
| $x$, $y$ | $T\{\cdot\}$ | filter design/system identification |
| $y$, $T\{\cdot\}$ | $x$ | inverse filtering/deconvolution |
| $y$만 주어짐 | $x$, $T\{\cdot\}$ | blind deconvolution |

## Norm

### Vector Norm

대표적인 $p$-norm:

$$
\|\mathbf{x}\|_p =
\left(\sum_i |x_i|^p\right)^{1/p}
$$

특히 least squares에서는 $L_2$ norm이 중요하다.

$$
\|\mathbf{x}\|_2^2 = \sum_i |x_i|^2
$$

### Matrix Norm

행렬 norm은 시스템이 입력을 얼마나 증폭하는지 나타낸다.

$$
\|H\| = \max_{\mathbf{x}\neq0}\frac{\|H\mathbf{x}\|}{\|\mathbf{x}\|}
$$

inverse problem에서는 condition number가 중요하다.

$$
\kappa(H)=\|H\|\|H^{-1}\|
$$

condition number가 크면 작은 오차가 해에서 크게 증폭된다.

## 체크포인트

- direct inverse는 이상적인 경우에만 안전하다.
- least squares는 정확히 맞추기보다 residual energy를 최소화한다.
- overdetermined 문제는 보통 LS로 안정적으로 풀 수 있다.
- underdetermined 문제는 prior나 regularization 없이는 해가 결정되지 않는다.
- inverse filtering은 수학적으로 가능해도 noise 때문에 실용적으로 불안정할 수 있다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **06. Linear Algebra Inverse**를 다루며, 이산시간 신호를 z-domain, frequency domain, filter structure, FFT 관점에서 분석하고 설계한다.
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
- **06. Linear Algebra Inverse**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [05. LTI System to Linear Algebra](05-lti-system-to-linear-algebra.md) · 다음: [07. Z-Transform Introduction](07-z-transform-introduction.md)
