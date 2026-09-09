---
layout: page
title: "11. 확산·광흡수·접촉·PN접합"
permalink: /studies/circuits/electronic-materials/11-diffusion-optical-absorption-contacts-pn-junction/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes) · 교재: Kasap Ch.5–6
- 이전: [10. 반도체 캐리어](10-intrinsic-extrinsic-semiconductors.md) · 다음: [12. PN 다이오드](12-pn-diode-operation-and-special-diodes.md)

{% raw %}
## 개요

```text
carrier diffusion (J ∝ dn/dx) + drift → total current, Einstein D/μ = kT/q
비균일 doping → built-in field. 광흡수 hν ≥ E_g, α로 E_g 추정
metal–semiconductor: Φ_M vs Φ_S → Schottky(rectifying) / Ohmic
PN junction: 확산·재결합 → depletion region, V_bi. depletion approximation
```

---

## 1. Carrier Diffusion

### Electron / Hole diffusion current
$$
J_{n,diff} = q D_n \frac{dn}{dx}, \qquad J_{p,diff} = -q D_p \frac{dp}{dx}
$$
- 전자 flux는 $-dn/dx$ 방향, 전류는 부호가 반대라 $+dn/dx$
- 정공은 양전하 → hole diffusion flux와 전류 방향이 같음

**Einstein relation**: $\;\dfrac{D_n}{\mu_n} = \dfrac{D_p}{\mu_p} = \dfrac{kT}{q} = V_T$ (상온 26 mV).

### Drift + Diffusion 결합
$$
J_n = q\mu_n n E + q D_n \frac{dn}{dx}, \qquad J_{total} = J_{drift} + J_{diff}
$$
광생성으로 특정 영역에 excess e–h pair가 많으면 농도구배 ↑ → diffusion current ↑.

## 2. 비균일 doping과 Built-in Potential

donor 농도가 위치에 따라 감소 → 전자가 고농도 → 저농도로 확산 → 빠져나간 쪽에 ionized donor(양전하) 노출 → **built-in electric field** → steady state에서 diffusion 이동 = built-in field drift.

## 3. Optical Absorption

### e–h pair 생성
$$
h\nu \ge E_g
$$
여기된 전자는 excess energy를 lattice vibration으로 잃고 CB 바닥 근처로 **thermalize** (CB 내 평균 에너지 $\sim \tfrac32 kT$).

### Absorption coefficient와 band gap
- $\alpha$는 photon energy/파장에 따라 변함
- $h\nu$가 $E_g$를 넘기 시작하면 흡수 급증 (absorption edge)
- DOS가 band edge부터 증가 → photon energy ↑ → 가능한 전이 상태 ↑
- **$\alpha$ vs $h\nu$ 측정 → band gap 추정** (Tauc plot: direct는 $(\alpha h\nu)^2$, indirect는 $(\alpha h\nu)^{1/2}$ vs $h\nu$)
- $I(x) = I_0 e^{-\alpha x}$ (Beer–Lambert), 침투 깊이 $1/\alpha$

## 4. Piezoresistivity

stress → resistivity 변화. 반도체는 band structure·carrier mobility가 stress에 민감해 효과 큼. force/pressure sensor, strain gauge, accelerometer, microphone. cantilever 지지부 stress를 piezoresistor가 저항 변화로 읽음.

## 5. Metal–Semiconductor Contact

- metal work function $\Phi_M$ = 고유
- semiconductor work function $\Phi_S$ = doping·$E_F$ 위치에 의존
- 두 $\Phi$ 관계 + band bending → I–V 특성

| 접촉 | I–V | 의미 |
|---|---|---|
| **Schottky** | rectifying | 한 방향 전류 우세, barrier contact |
| **Ohmic** | linear $V = IR$ | 양방향 carrier 이동 쉬움, non-rectifying |

## 6. Schottky Contact

### n-type 조건
$\Phi_M > \Phi_S$ → Schottky contact. semiconductor 쪽에 **electron depletion region** + band bending. barrier height $\Phi_B = \Phi_M - \chi$ (ideal Schottky–Mott). 열평형에서 $E_F$ 평탄, net carrier flow 0.

### Doping 의존성
doping ↑ → ideal barrier height 자체는 크게 안 변하지만 **depletion/barrier width 감소** → 매우 높은 doping ($> 10^{17}\,\text{cm}^{-3}$)에서 barrier가 얇아져 **tunneling** 가능 → 양방향 carrier action (Ohmic처럼).

### Schottky effect (image-force lowering)
외부 전기장이 표면 장벽을 낮춰 thermionic emission 증가:
$$
\Phi_{eff} = \Phi - \sqrt{\frac{e^3 E}{4\pi\varepsilon_0}}
$$

### 응용
- Schottky diode: forward drop 낮음 (**~0.2–0.3 V** vs PN diode ~0.6–0.7 V), switching 빠름 (minority carrier storage 없음)
- reverse-biased Schottky photodiode → 빠른 photodetector

## 7. Ohmic Contact

### n-type 조건
$\Phi_M < \Phi_S$ → **electron accumulation region**, barrier 거의 없음 → carrier 이동 쉬움. MOSFET의 source/drain/gate contact는 carrier 주입·추출용 Ohmic contact로 설계 (실제로는 heavily doped + tunneling으로 구현).

### 열전 응용
금속–반도체 Ohmic contact에서 전류 방향에 따라 heat absorption/dissipation 위치가 달라짐 (**Peltier effect**). thermoelectric device는 넓은 Ohmic contact 면적 + ceramic layer(전기 절연 + 열 전달).

## 8. PN Junction 형성

### 전기적 중성
- p-type: acceptor ion 수 = hole 수 → 중성
- n-type: donor ion 수 = free electron 수 → 중성

### 접합 직후
- p쪽 hole → n쪽 확산, n쪽 electron → p쪽 확산
- 접합 근처에서 재결합
- mobile carrier 사라진 영역에 **고정 ionized acceptor(음) + donor(양)** 남음 → **depletion region (공간전하 영역)**

### Built-in field와 band diagram
- depletion 고정 전하 → n쪽 양, p쪽 음 → electric field (추가 확산을 막는 방향)
- 열평형: **$E_F$가 전체 junction에서 일정**
- band diagram에 $V_{bi}$ 만큼 band bending: $\;V_{bi} = V_T \ln\dfrac{N_A N_D}{n_i^2}$

## 9. Depletion Approximation

$$
\rho = \begin{cases}
-q N_A & -x_p \le x \le 0 \\
+q N_D & 0 \le x \le x_n \\
0 & \text{depletion 밖 (quasi-neutral)}
\end{cases}
$$
- 전하 중성: $N_A x_p = N_D x_n$
- 이 근사로 $\rho \to E(x)$ (Poisson 1차 적분) $\to V(x)$ (2차 적분)의 **closed-form** 해:
$$
W = x_p + x_n = \sqrt{\frac{2\varepsilon_s}{q}\left(\frac{1}{N_A} + \frac{1}{N_D}\right)V_{bi}}
$$

---

## 복습 질문

- electron diffusion current와 hole diffusion current의 부호 차이, Einstein relation $D/\mu = kT/q$는?
- 비균일 doping에서 built-in field가 생기는 이유는?
- absorption coefficient $\alpha$로 band gap을 측정하는 직관(absorption edge, Tauc plot)은?
- Schottky($\Phi_M > \Phi_S$)와 Ohmic($\Phi_M < \Phi_S$) contact의 조건, Schottky diode가 PN diode보다 forward drop이 낮고 빠른 이유는?
- PN junction에서 depletion region·$V_{bi}$가 형성되는 순서, depletion approximation의 가정과 그로부터 $W$를 구하는 흐름은?
{% endraw %}

---

이전: [10. 반도체 캐리어](10-intrinsic-extrinsic-semiconductors.md) · 다음: [12. PN 다이오드](12-pn-diode-operation-and-special-diodes.md)
