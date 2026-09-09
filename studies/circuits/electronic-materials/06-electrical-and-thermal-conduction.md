---
layout: page
title: "06. 전기·열 전도 (Electrical & Thermal Conduction)"
permalink: /studies/circuits/electronic-materials/06-electrical-and-thermal-conduction/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes) · 교재: Kasap Ch.2
- 이전: [05. 터널링·흑체·레이저](05-tunneling-blackbody-laser.md) · 다음: [07. Si 박막 결정화](07-silicon-thin-film-crystallization-si.md)

{% raw %}
## 개요

```text
drift 전도 J = nqv_d → μ = eτ/m*, σ = neμ
Matthiessen 법칙 ρ = ρ_T + ρ_I, grain boundary·Nordheim ρ_I = CX(1-X)
Hall effect → carrier type/concentration/mobility
열전도: 금속(전자) vs 비금속(phonon), Wiedemann–Franz–Lorenz
```

---

## 1. Conduction Electron

- **금속**: 전도 전자가 이미 부분적으로 찬 밴드에 존재 → 작은 전기장에도 이동 (Fermi 준위 근처).
- **반도체**: 전도대 전자 + 가전자대 정공이 carrier.
- **이온 결정·유리**: mobile ion (예: Na⁺)이 있으면 전도도 ≠ 0.

## 2. Drift 전도

$$
J = n q v_d, \qquad v_d = \mu E, \qquad \mu = \frac{e\tau}{m^*}, \qquad \sigma = n q \mu = \frac{1}{\rho}
$$
($\tau$ = 평균 자유시간, $m^*$ = 유효질량.)

- 전기장 없을 때: 열운동으로 무작위, 평균 drift = 0.
- 전기장 있을 때: 무작위 열운동 위에 작은 $v_d$ → 순전류.
- 평균 자유행로: $\ell = u\tau$ ($u$ = 열속도).
- 반도체: $\sigma = q(n\mu_n + p\mu_p)$.

## 3. 박막 저항률과 Matthiessen 법칙

### Grain boundary scattering
다결정 박막에서 grain boundary가 전자를 산란 → 저항률 ↑. grain이 매우 작으면 $\ell$이 grain 지름으로 제한 → 같은 재료도 결정립 크기·공정 이력에 따라 저항률이 다름.

### Matthiessen 법칙
독립적인 산란 과정의 저항률은 **더해진다**:
$$
\rho = \rho_T + \rho_I \;(+\, \rho_{GB} + \rho_{disl} + \cdots)
$$
$\rho_T$ = 열진동(phonon) 산란, $\rho_I$ = 불순물 산란.

## 4. 금속 저항률의 온도 의존성

충분히 높은 온도: $\;\rho \propto T$.
- Cu: 100 K 이상 $\rho \propto T$, 100 K 이하 더 급함, **10 K 이하 → residual resistivity $\rho_R$** (불순물·결함으로 온도가 낮아도 남음).
- **TCR** (thermal coefficient of resistivity): $\alpha_0 = \dfrac{1}{\rho_0}\dfrac{d\rho}{dT}$. NiCr = 높은 $\rho$ + 작은 TCR → heating wire.

## 5. Nordheim 법칙 (합금 저항률)

고용 합금에서 불순물이 host lattice를 흐트러뜨려 산란:
$$
\rho_I = C\,X(1 - X)
$$
$X$ = solute 농도, $C$ = Nordheim coefficient (solute–solvent 크기·퍼텐셜 차 클수록 큼). dilute alloy($X \ll 1$): $\rho_I \approx CX$. alloy scattering이 커지면 $\rho_I > \rho_T$ → 저항률의 온도 의존성 약해짐.

**열처리**: 급랭 amorphous/변형 금속은 무질서 많아 $\rho$ 높음; annealing으로 결정성 회복 → $\rho$ ↓.

**Eutectic**: 두 원소 합금의 최저 녹는점 조성. solubility limit까지 조성 ↑ → Nordheim으로 $\rho$ ↑; 그 이후 두 상 공존 → 단상 법칙만으로 설명 어려움.

## 6. Hall Effect

### 원리
$x$ 방향 전류 + $z$ 방향 자기장 $B_z$ → carrier가 Lorentz 힘 $\vec{F} = q\vec{v}\times\vec{B}$로 $y$ 방향 분리 → **Hall electric field $E_H$**.

### Hall coefficient
$$
R_H = \frac{E_y}{J_x B_z}, \qquad
\text{전자: } R_H = -\frac{1}{en}, \qquad
\text{정공: } R_H = +\frac{1}{ep}
$$
- $R_H < 0$ → **n-type**, $R_H > 0$ → **p-type**
- carrier 농도는 $|R_H|$에서

### Hall mobility
$$
\sigma = q\mu n \;\Rightarrow\; \mu = |R_H|\,\sigma
$$
(강의는 n-type 전자 기준 $\mu = -R_H\sigma$ 형태.)

## 7. 열전도

| | Carrier | |
|---|---|---|
| **금속** | conduction electron | $\sigma$ 크면 $\kappa$도 큼 |
| **비금속** | **phonon** (격자진동) | 결합 강도·coupling·결함 산란에 좌우 |

### Ohm ↔ Fourier
$$
I = -A\sigma\frac{dV}{dx}, \qquad Q' = -A\kappa\frac{dT}{dx}
$$
전위구배 ↔ 온도구배가 각각 driving force.

### Wiedemann–Franz–Lorenz
$$
\kappa = C_{WFL}\,\sigma\,T, \qquad \frac{\kappa}{\sigma T} = L \approx 2.44\times10^{-8}\,\text{W·Ω/K}^2 \;(\text{Lorenz number})
$$
순수 금속에서 $\sigma \propto 1/T$ 이면 $\kappa$가 온도에 비교적 일정.

### 열저항
$$
Q' = \frac{\Delta T}{\theta}
$$
$\theta$ = 열저항 (전기저항과 유사, 직렬·병렬 규칙 적용).

---

## 복습 질문

- $J = nqv_d$, $\mu = e\tau/m^*$, $\sigma = nq\mu$의 연결과, 전기장 유무에서 전자의 운동 차이는?
- Matthiessen 법칙의 물리적 의미와, grain boundary가 박막 저항률을 키우는 이유는?
- Nordheim 법칙 $\rho_I = CX(1-X)$의 해석, alloy scattering이 커지면 온도 의존성이 왜 약해지나?
- Hall coefficient 부호로 n/p type을 구분하는 법, carrier 농도·mobility를 어떻게 얻나?
- 금속과 비금속의 열전도 carrier 차이, Wiedemann–Franz–Lorenz 법칙이 말하는 것은?
{% endraw %}

---

이전: [05. 터널링·흑체·레이저](05-tunneling-blackbody-laser.md) · 다음: [07. Si 박막 결정화](07-silicon-thin-film-crystallization-si.md)
