---
layout: page
title: "10. Wiener Optimal Filter"
permalink: /studies/signals/dsp/10-wiener-optimal-filter/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Signal_Processing/lecture_notes/10%20Wiener%20Optimal%20Filter.md)

{% raw %}
## 학습 목표

이 자료는 noisy observation으로부터 원 신호를 추정하는 Wiener least squares filter를 다룬다.

- noisy observation model
- prediction/inverse filter
- mean square error 최소화
- orthogonality principle
- autocorrelation, cross-correlation, power spectrum
- Wiener filter의 주파수 영역 형태

## Observation Model

원 신호 $$x[n]$$가 시스템 $$h[n]$$를 거치고 잡음 $$v[n]$$가 더해져 관측 신호 $$y[n]$$가 된다고 하자.

$$
y[n]=h[n]*x[n]+v[n]
$$

주파수 영역:

$$
Y(e^{j\omega})=H(e^{j\omega})X(e^{j\omega})+V(e^{j\omega})
$$

목표는 $$y[n]$$로부터 $$x[n]$$를 추정하는 필터 $$\hat{h}[n]$$를 찾는 것이다.

$$
\hat{x}[n]=\hat{h}[n]*y[n]
$$

## Prediction Error

추정 오차:

$$
e[n]=x[n]-\hat{x}[n]
$$

Wiener filter는 평균제곱오차를 최소화한다.

$$
J=E\{|e[n]|^2\}
=E\{|x[n]-\hat{h}[n]*y[n]|^2\}
$$

## Orthogonality Principle

최적 추정에서는 오차가 관측 데이터의 모든 사용된 성분과 직교한다.

$$
E\{e[n]y^*[n-k]\}=0
$$

따라서

$$
E\{(x[n]-\hat{x}[n])y^*[n-k]\}=0
$$

이고, 이를 상관함수로 쓰면 Wiener-Hopf 방정식이 된다.

## Correlation 형태

cross-correlation:

$$
r_{xy}[k]=E\{x[n]y^*[n-k]\}
$$

autocorrelation:

$$
r_{yy}[k]=E\{y[n]y^*[n-k]\}
$$

최적 필터는

$$
r_{xy}[k]=\hat{h}[k]*r_{yy}[k]
$$

형태의 방정식을 만족한다.

## Frequency-Domain Wiener Filter

상관함수의 Fourier transform은 power spectrum이다.

$$
S_{xx}(e^{j\omega})=\mathcal{F}\{r_{xx}[k]\}
$$

일반적인 Wiener filter:

$$
\hat{H}(e^{j\omega})
=\frac{S_{xy}(e^{j\omega})}{S_{yy}(e^{j\omega})}
$$

관측 모델 $$y=h*x+v$$이고 $$x$$와 $$v$$가 uncorrelated이면

$$
S_{yy}=|H|^2S_{xx}+S_{vv}
$$

$$
S_{xy}=S_{xx}H^*
$$

따라서

$$
\hat{H}(e^{j\omega})
=
\frac{H^*(e^{j\omega})S_{xx}(e^{j\omega})}
{|H(e^{j\omega})|^2S_{xx}(e^{j\omega})+S_{vv}(e^{j\omega})}
$$

이는 inverse filter와 noise suppression이 결합된 형태이다.

## Least Squares Inverse와 비교

단순 inverse filter:

$$
\hat{X}=\frac{Y}{H}
$$

문제:

- $$H$$가 작아지는 주파수에서 noise가 크게 증폭된다.
- zero 또는 near-zero가 있으면 불안정하다.

Wiener filter:

$$
\hat{X}
=
\frac{H^*S_{xx}}{|H|^2S_{xx}+S_{vv}}Y
$$

잡음 전력이 큰 주파수에서는 gain을 낮춰 noise amplification을 억제한다.

**숫자 예 — 좋은 주파수 vs 나쁜 주파수**: 어떤 $$\omega$$에서 $$S_{xx}=1$$, $$S_{vv}=0.5$$로 고정하고 채널 크기만 바꿔보자.

| $$|H|$$ | 단순 inverse $$1/H$$ | Wiener $$\hat H=\dfrac{H^*S_{xx}}{|H|^2S_{xx}+S_{vv}}$$ |
|---:|---:|---:|
| $$2$$ (좋은 채널) | $$0.5$$ | $$2/(4+0.5)=0.444$$ |
| $$0.1$$ (나쁜 채널) | $$10$$ | $$0.1/(0.01+0.5)\approx0.196$$ |

채널이 좋을 때($$|H|=2$$)는 두 값이 비슷하지만(0.5 vs 0.444), 채널이 거의 죽은 주파수($$|H|=0.1$$)에서는 단순 inverse가 $$10$$배 증폭을 요구하는 반면 Wiener filter는 $$0.196$$으로 오히려 gain을 낮춘다 — "신호가 잘 안 보이는 주파수는 굳이 복원하려 애쓰지 말고 억제하라"는 것이 Wiener filter의 핵심 전략이다.

## 구현 이슈

강의자료는 prediction inverse filter의 구현 문제를 강조한다.

- 실제로 $$S_{xx}$$와 $$S_{vv}$$를 정확히 모르는 경우가 많다.
- power spectrum을 추정해야 한다.
- filter가 causal/stable하게 구현 가능한지 확인해야 한다.
- frequency-domain 식을 시간 영역 FIR/IIR 구조로 근사해야 할 수 있다.

## 체크포인트

- Wiener filter는 MSE를 최소화하는 optimal linear filter이다.
- 핵심 원리는 “오차가 관측 공간과 직교한다”는 orthogonality principle이다.
- 단순 inverse와 달리 noise spectrum을 고려한다.
- $$S_{vv}$$가 0이면 inverse filter에 가까워지고, $$S_{vv}$$가 크면 해당 주파수 성분을 억제한다. $$|H|=0.1$$인 나쁜 채널에서 단순 inverse(10배 증폭)와 Wiener(0.196)가 왜 이렇게 다른지 설명할 수 있는가?

{% endraw %}

---

이전: [09. Structures and Parametric Modeling](09-structures-and-parametric-modeling.md) · 다음: [11. Digital Filter Design](11-digital-filter-design.md)
