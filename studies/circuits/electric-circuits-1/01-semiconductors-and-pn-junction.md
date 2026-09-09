---
layout: page
title: "01. 반도체와 PN 접합"
permalink: /studies/circuits/electric-circuits-1/01-semiconductors-and-pn-junction/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/01%20%EB%B0%98%EB%8F%84%EC%B2%B4%EC%99%80%20PN%20%EC%A0%91%ED%95%A9.md) · 교재: Razavi, *Fundamentals of Microelectronics* Ch.2

{% raw %}
## 한눈에 보기

전자회로 1의 출발점. 원자 결합 → 도핑 → 캐리어 농도 → drift/diffusion 전류 → PN 접합의 built-in potential → 순방향/역방향 바이어스 → 다이오드 전류식.

```text
원자와 공유결합 → intrinsic/extrinsic semiconductor → n형/p형 도핑
→ drift/diffusion current → PN junction → built-in potential → diode equation
```

전자회로 1은 기초회로이론의 선형 수동소자로 설명 못 하는 **능동소자**(다이오드, BJT, MOSFET, 증폭기, 전류원/전류미러, CMOS 인버터)를 다룬다. 회로 문제는 **동작 영역을 먼저 판단하고 그 영역의 등가모델을 적용**하는 순서가 핵심이다.

---

## 1. 원자와 최외각 전자

원자의 화학적 성질은 최외각 전자(valence electron)로 결정된다. 원자는 안정한 전자 배치를 위해 전자를 잃거나(→ 양이온) 얻거나(→ 음이온) **공유**한다.

- Na → $Na^+$ (전자 1개 잃음), Cl → $Cl^-$ (전자 1개 얻음)
- Ne/He/Ar: 이미 안정한 최외각 구조 (비활성)
- **Si**: 4족, 최외각 전자 4개 → 이웃 Si와 **공유결합**해 다이아몬드형 결정 구조

## 2. 공유결합과 캐리어

Si 결정에서 전자가 공유결합에 묶여 있으면 이동 불가. **에너지(열·빛)를 받으면 결합이 끊겨** 자유전자가 생기고 그 자리에 **정공(hole)**이 남는다.

| 캐리어 | 전하 |
|---|---|
| 자유전자 | 음전하 |
| 정공 | 양전하처럼 거동 |

밴드 관점: 결합 전자 = valence band, 자유전자 = conduction band, 그 사이가 **band gap** $E_g$ (Si ≈ 1.12 eV). 전자–정공 쌍 생성률은 온도에 지수적으로 의존한다.

## 3. 캐리어 농도

| 기호 | 의미 |
|---|---|
| $n$ / $p$ | 전자 / 정공 농도 |
| $n_i$ | intrinsic 캐리어 농도 |
| $N_D$ / $N_A$ | donor / acceptor 도핑 농도 |

순수 반도체: 전자·정공이 쌍생성 → $n_i = p_i$.

**질량작용 법칙** (열평형에서 항상 성립):

$$
np = n_i^2
$$

상온 Si: $n_i \approx 1.08\times10^{10}\,\text{cm}^{-3}$ — Si 원자 밀도($5\times10^{22}\,\text{cm}^{-3}$)에 비해 극히 작다 → **도핑 필요**. $n_i^2 = B\,T^3 e^{-E_g/kT}$ 형태로 온도에 강하게 의존.

## 4. 도핑

### n형 (donor, 5족 예: P, As)
공유결합 후 전자 1개가 남음 → 다수 캐리어 = 전자.
$N_D \gg n_i$ 이면
$$
n \approx N_D, \qquad p \approx \frac{n_i^2}{N_D}
$$

### p형 (acceptor, 3족 예: B)
전자가 부족한 자리 → 다수 캐리어 = 정공.
$N_A \gg n_i$ 이면
$$
p \approx N_A, \qquad n \approx \frac{n_i^2}{N_A}
$$

> **예**: $N_D = 10^{16}$ → $n = 10^{16}$, $p = (1.08\times10^{10})^2/10^{16} \approx 1.2\times10^{4}\,\text{cm}^{-3}$. minority 농도가 majority보다 12자리 작다.

도핑은 캐리어 농도를 높일 뿐 아니라 **전기적 중성**을 유지한다: $n + N_A^- = p + N_D^+$.

---

## 5. Drift Current (전기장에 의한 전류)

전기장 $E$ 안에서 캐리어가 표류(drift). 속도는 저농도 field에서 $E$에 비례:

$$
v_e = -\mu_n E, \qquad v_h = \mu_p E
$$

$\mu_n, \mu_p$ = 이동도(mobility). Si에서 $\mu_n \approx 1350$, $\mu_p \approx 480\,\text{cm}^2/\text{V·s}$ (전자가 더 빠름).

전류밀도:
$$
J_e = q n \mu_n E, \qquad J_h = q p \mu_p E
$$
$$
\boxed{J_{\text{drift}} = q(n\mu_n + p\mu_p)\,E} \quad\Rightarrow\quad \sigma = q(n\mu_n + p\mu_p),\;\; \rho = 1/\sigma
$$

고전계에서는 $v$가 $v_{sat}\approx10^7\,\text{cm/s}$로 포화(velocity saturation).

## 6. Diffusion Current (농도 기울기에 의한 전류)

캐리어는 농도 높은 곳 → 낮은 곳으로 확산.

$$
J_{e,\text{diff}} = q D_n \frac{dn}{dx}, \qquad
J_{h,\text{diff}} = -q D_p \frac{dp}{dx}
$$
$$
J_{\text{diff}} = q D_n \frac{dn}{dx} - q D_p \frac{dp}{dx}
$$

부호 차이는 캐리어 전하 부호 × conventional current 방향 때문. (전자는 −전하라 농도 감소 방향으로 확산해도 전류는 +방향.)

## 7. Einstein Relation

drift와 diffusion 계수는 독립이 아니다:

$$
\frac{D_n}{\mu_n} = \frac{D_p}{\mu_p} = \frac{kT}{q} \equiv V_T
$$

$V_T$ = **thermal voltage**, 상온(300 K)에서 $\approx 26\,\text{mV}$. 다이오드·BJT 식에 계속 등장한다.

---

## 8. PN 접합 — 세 가지 상태

| 상태 | 특징 |
|---|---|
| **평형** (bias 0) | built-in potential $V_0$, 전체 전류 0 |
| **순방향 바이어스** | 장벽 낮아짐, diffusion 전류 지수적 증가 |
| **역방향 바이어스** | depletion 확장, 가변 커패시터 거동, 미소 누설전류 |

### 평형과 depletion region

p·n을 접합하면 농도차로 전자는 p쪽, 정공은 n쪽으로 확산 → 접합 근처에서 재결합 → **움직이지 못하는 이온만 남음** = depletion region(공핍층).

- n쪽: 양전하 donor 이온
- p쪽: 음전하 acceptor 이온
- 고정 이온 → 내부 전기장 → drift 전류(확산과 반대 방향)

평형에서 **diffusion 전류 = drift 전류** → 순 전류 0.

### Built-in potential

$$
\boxed{V_0 = V_T \ln\!\left(\frac{N_A N_D}{n_i^2}\right)}
$$

> **예**: $N_A = 2\times10^{16}$, $N_D = 4\times10^{16}$, $n_i = 1.08\times10^{10}$, $V_T = 26\,\text{mV}$
> $V_0 = 0.026 \times \ln\!\dfrac{8\times10^{32}}{1.17\times10^{20}} \approx 0.026 \times 29.5 \approx \mathbf{768\ mV}$

도핑을 10배 올려도 $V_0$는 $2V_T\ln10 \approx 120\,\text{mV}$만 증가 (로그 의존).

### 공핍층 폭 (참고)

$$
W = \sqrt{\frac{2\varepsilon_s}{q}\left(\frac{1}{N_A} + \frac{1}{N_D}\right)(V_0 - V_D)}
$$

$V_D$ = 인가 전압(순방향 +, 역방향 −). 순방향이면 $W$ 감소, 역방향이면 $W$ 증가.

## 9. 역방향 바이어스 — 접합 커패시턴스

역방향 전압 $V_R$을 걸면 depletion이 넓어지고 캐리어가 접합을 못 넘어 **전류 ≈ 0** (미소 saturation 누설 $I_S$ + 온도 민감).

depletion = 절연체, 양쪽 중성영역 = 도체 → **커패시터**:

$$
C_j = \varepsilon \frac{S}{W}, \qquad
C_j(V_R) = \frac{C_{j0}}{\sqrt{1 + V_R/V_0}}
$$

$V_R \uparrow$ → $W \uparrow$ → $C_j \downarrow$. 이 **전압 가변 커패시터**가 **varactor(varicap) diode**의 원리 (VCO, 튜너).

역방향 전압이 매우 커지면 **breakdown**: Zener(터널링, 낮은 전압, 음의 온도계수) 또는 avalanche(충돌 이온화, 높은 전압, 양의 온도계수).

## 10. 순방향 바이어스 — Shockley 식

순방향 전압은 내부 장벽을 낮추고 depletion을 좁힌다 → 다수 캐리어가 접합을 넘어 minority injection → diffusion 전류 급증.

$$
\boxed{I = I_S\!\left(e^{V_F/V_T} - 1\right)}
$$

$V_F \gtrsim 4V_T$ 이면 $-1$ 무시:
$$
I \approx I_S\, e^{V_F/V_T}
$$

- $I_S$ (역포화 전류): 접합 면적·도핑·$n_i^2$에 비례 → **온도에 매우 민감**, 대략 5°C마다 2배.
- 전류를 10배 늘리려면 $V_F$는 $V_T\ln10 \approx 60\,\text{mV}$만 증가 → 순방향 강하가 "거의 일정"(≈0.7 V)해 보이는 이유.
- $\partial V_F/\partial T \approx -2\,\text{mV/°C}$ (일정 전류에서).

## 11. 실용적 다이오드 모델

| 모델 | 켜짐 | 꺼짐 | 용도 |
|---|---|---|---|
| **Ideal** | 단락, $V_D = 0$ | 개방, $I_D = 0$ | 빠른 정성 해석 |
| **Constant-voltage** | $V_D = V_{D,on}$ (≈0.7~0.8 V) | $I_D = 0$ | 정확도·복잡도 타협 (표준) |
| **Exponential** | $I = I_S(e^{V/V_T}-1)$ | — | 정밀 해석, 반복 계산 |

---

## 핵심 정리

- 반도체 전류 = **drift**(전기장) + **diffusion**(농도 기울기), 둘은 Einstein 관계 $D/\mu = V_T$로 연결.
- 도핑으로 캐리어 농도 제어: n형 → 전자 다수, p형 → 정공 다수. 항상 $np = n_i^2$.
- PN 접합 평형: diffusion = drift, $V_0 = V_T\ln(N_A N_D/n_i^2) \approx 0.7\text{–}0.8\,\text{V}$.
- 역방향: depletion 확장 → $C_j = C_{j0}/\sqrt{1+V_R/V_0}$ (varactor). 큰 역전압 → breakdown.
- 순방향: $I \approx I_S e^{V_F/V_T}$, 10× 전류 = +60 mV, $I_S$는 5°C당 2배.

## 복습 질문

- $np = n_i^2$이 성립하는 조건(열평형)과, 도핑 후 minority 농도를 구하는 법은?
- drift와 diffusion 전류의 발생 원인·식, 그리고 Einstein relation이 말하는 것은?
- depletion region이 형성되는 과정과, 평형에서 순 전류가 0인 이유는?
- $V_0$ 식에 도핑을 10배 넣으면 얼마나 변하나? built-in potential ≈ 768 mV 예를 재현할 수 있나?
- 순방향 전류를 10배 키우는 데 필요한 $\Delta V_F$는? 다이오드 3가지 모델의 차이는?
{% endraw %}

---

다음: [02. 다이오드 응용](02-diode-applications.md)
