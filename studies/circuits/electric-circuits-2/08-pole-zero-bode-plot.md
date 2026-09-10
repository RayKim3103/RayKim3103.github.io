---
layout: page
title: "08. Pole·Zero & Bode Plot"
permalink: /studies/circuits/electric-circuits-2/08-pole-zero-bode-plot/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `10 Pole Zero Bode Plot` 보강

{% raw %}
## 개요

회로의 frequency response는 s-domain transfer function의 **pole·zero**로 결정된다. Bode plot = $$20\log_{10}|H(j\omega)|$$와 phase를 log frequency 축에 그린 것. **pole**: magnitude slope $$-20\,\text{dB/dec}$$, phase $$-90°$$. **zero**: $$+20\,\text{dB/dec}$$, $$+90°$$.

---

## 1. s-Domain 해석

시간 영역 미분방정식 → Laplace → s-domain 대수 문제.
$$
Z_C = \frac{1}{sC}, \qquad Z_L = sL
$$
sinusoidal steady state: $$s = j\omega$$.

## 2. Transfer Function

$$
H(s) = \frac{V_{out}(s)}{V_{in}(s)} = A\,\frac{\prod(s - z_i)}{\prod(s - p_i)}
$$
$$z_i$$ = zeros, $$p_i$$ = poles. filter order = denominator 차수.

## 3. dB Scale

$$
\text{dB}_{power} = 10\log_{10}\frac{P_{out}}{P_{in}}, \qquad
\text{dB}_{voltage} = 20\log_{10}\frac{V_{out}}{V_{in}}
$$
(power ∝ voltage² 이므로 20 vs 10.)

## 4. 기본 요소별 Bode 효과

| 요소 | $$H(s)$$ | magnitude | phase |
|---|---|---|---|
| **Zero at origin** | $$s$$ | $$+20\,\text{dB/dec}$$ | $$+90°$$ |
| **Pole at origin** | $$1/s$$ | $$-20\,\text{dB/dec}$$ | $$-90°$$ |
| **Real zero** | $$1 + s/\omega_z$$ | $$\omega\ll\omega_z$$: 0; $$\omega=\omega_z$$: $$+3$$ dB; $$\omega\gg\omega_z$$: $$+20\,\text{dB/dec}$$ | $$0° \to +90°$$ ($$\omega_z/10 \sim 10\omega_z$$) |
| **Real pole** | $$1/(1 + s/\omega_p)$$ | $$\omega=\omega_p$$: $$-3$$ dB; $$\omega\gg\omega_p$$: $$-20\,\text{dB/dec}$$ | $$0° \to -90°$$ |

## 5. CS Amplifier 예 (single pole)

MOS 자체 고주파 응답 무시, output load $$C_L$$만:
$$
H(s) = \frac{-g_m R_D}{1 + s R_D C_L}
$$
$$
\omega_p = \frac{1}{R_D C_L}, \qquad A_{v0} = -g_m R_D
$$

## 6. Bode Plot 작성 절차

1. transfer function을 pole–zero 형태로 정리
2. DC gain(또는 기준 gain)을 dB로 표시
3. 각 zero에서 slope $$+20\,\text{dB/dec}$$ 추가
4. 각 pole에서 slope $$-20\,\text{dB/dec}$$ 추가
5. phase는 각 pole/zero의 decade 전후에서 부드럽게 변화

---

## 핵심 정리

- $$Z_C = 1/sC$$, $$Z_L = sL$$; sinusoidal steady state는 $$s = j\omega$$.
- voltage ratio는 $$20\log$$, power ratio는 $$10\log$$.
- pole: $$-20\,\text{dB/dec}$$ & $$-90°$$; zero: $$+20\,\text{dB/dec}$$ & $$+90°$$; corner에서 $$\mp 3$$ dB.
- single-pole CS: $$\omega_p = 1/(R_D C_L)$$, $$A_{v0} = -g_m R_D$$.

## 복습 질문

- capacitor/inductor의 s-domain impedance와, $$s=j\omega$$의 의미는?
- real pole과 real zero가 magnitude slope·phase에 주는 영향을 corner frequency 기준으로 설명할 수 있나?
- single-pole CS amplifier의 pole $$1/(R_D C_L)$$을 바로 도출할 수 있나?
{% endraw %}

---

이전: [07. Differential Amplifiers](07-differential-amplifiers.md) · 다음: [09. MOSFET 고주파 모델](09-mosfet-high-frequency-model.md)
