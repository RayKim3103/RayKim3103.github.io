---
layout: page
title: "03. Common-Source 증폭기 (CS)"
permalink: /studies/circuits/electric-circuits-2/03-common-source-amplifier-cs/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `04 Common-Source Amplifier` 보강

{% raw %}
## 개요

CS 증폭기는 MOSFET의 $$g_m v_{gs}$$ 전류원 특성으로 입력 전압을 출력 전압/전류로 변환. 기본 gain $$-g_m R_D$$, $$r_o$$ 포함 시 $$-g_m(R_D\parallel r_o)$$. source degeneration은 gain을 낮추는 대신 안정성·선형성을 준다.

---

## 1. 증폭기의 네 가지 유형

| 입력 | 출력 | 유형 | 이상적 $$R_{in}$$ | 이상적 $$R_{out}$$ |
|---|---|---|---:|---:|
| V | V | Voltage amplifier | ∞ | 0 |
| I | I | Current amplifier | 0 | ∞ |
| V | I | **Transconductance amplifier** | ∞ | ∞ |
| I | V | Transimpedance amplifier | 0 | 0 |

CS는 특히 **transconductance amplifier**에 잘 맞는다 (gate 입력 → drain 전류).

## 2. 기본 CS 증폭기

small-signal:
$$
v_{out} = -g_m v_{in} R_D \;\Rightarrow\; A_v = -g_m R_D
$$
$$
R_{in} = \infty \quad(\text{gate 절연}), \qquad R_{out} = R_D
$$

**부호 −**: $$v_{gs}\uparrow \to I_D\uparrow \to R_D$$ 강하 ↑ $$\to V_D\downarrow$$ (위상 반전).

## 3. Channel-Length Modulation 포함

$$
A_v = -g_m(R_D \parallel r_o), \qquad R_{out} = R_D \parallel r_o, \qquad R_{in} = \infty
$$
$$r_o$$가 작을수록 gain·output resistance 감소.

## 4. CS as Transconductance Amplifier

출력 포트를 등가적으로 보면 $$i_{out} = g_m v_{in}$$ → $$G_m = g_m$$. 전압 gain은 이 전류가 output resistance를 흐른 결과.

## 5. PMOS Load CS (active load)

저항 대신 PMOS current-source load → IC에서 면적·정확도·구현성 향상:
$$
A_v = -g_{m1}(r_{o1} \parallel r_{o2}), \qquad R_{out} = r_{o1} \parallel r_{o2}, \qquad R_{in} = \infty
$$
$$-g_m r_o$$ 수준의 **intrinsic gain**에 가까워진다.

## 6. CS with Source Degeneration

source에 $$R_S$$ → **local negative feedback**.

$$r_o$$ 무시:
$$
A_v = -\frac{g_m R_D}{1 + g_m R_S}, \qquad R_{in} = \infty, \qquad R_{out} = R_D
$$
$$g_m R_S \gg 1$$:
$$
A_v \approx -\frac{R_D}{R_S}
$$

| 장점 | 단점 |
|---|---|
| gain이 $$g_m$$ 변화에 둔감 | voltage gain 감소 |
| 선형성 향상 | |
| bias 안정성 향상 | |

effective transconductance $$G_m = \dfrac{g_m}{1 + g_m R_S}$$.

---

## 핵심 정리

- 기본 CS: $$A_v = -g_m R_D$$ (위상 반전), $$R_{in} = \infty$$, $$R_{out} = R_D$$.
- $$r_o$$ 포함: $$A_v = -g_m(R_D \parallel r_o)$$. active load면 $$-g_m(r_{o1}\parallel r_{o2})$$.
- Source degeneration: $$A_v = -g_m R_D/(1+g_m R_S) \to -R_D/R_S$$, $$G_m = g_m/(1+g_m R_S)$$. gain↓, 선형성·안정성↑.

## 복습 질문

- 증폭기 4유형과 각각의 이상적 $$R_{in}$$·$$R_{out}$$, CS가 어디에 맞는가?
- CS gain 부호가 −인 이유를 소신호 등가회로로 설명할 수 있나?
- source degeneration의 gain 식과 $$g_m R_S \gg 1$$ 근사, 그리고 왜 "local negative feedback"인가?
{% endraw %}

---

이전: [02. MOS 소자 특성](02-mos-device-characteristics.md) · 다음: [04. Source Follower & Common-Gate](04-source-follower-and-common-gate-sf-cg.md)
