---
layout: page
title: "13. 음귀환 (Feedback) — 기초 · I/O 저항 개선"
permalink: /studies/circuits/electric-circuits-2/13-feedback/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `21 Feedback` + `24 IO Resistance Improvement 1` + `25 IO Resistance Improvement 2` **통합**

{% raw %}
## 개요

Feedback = output의 일부를 input으로 되돌림. **Negative feedback**은 closed-loop gain을 $$A/(1+KA)$$로 낮추는 대신 **gain 안정화 · bandwidth 확장 · I/O 저항을 이상적 조건에 근접**시킨다. 핵심은 loop gain $$T = KA$$.

```text
Y/X = A/(1+KA) → gain desensitization + bandwidth extension
+ topology별 Rin/Rout 개선 (voltage / current / transconductance / transimpedance)
```

---

# Part 1. Feedback 기초

## 1. Block Diagram

$$
Y = A\,E, \qquad E = X - K Y \;\Rightarrow\; \boxed{\frac{Y}{X} = \frac{A}{1 + KA}}
$$
- $$A$$: open-loop gain · $$K$$: feedback factor · $$KA = T$$: **loop gain**

## 2. Negative Feedback 조건

output이 input error를 줄이는 방향으로 되돌아옴. $$KA \gg 1$$ 이면:
$$
\frac{Y}{X} \approx \frac{1}{K}, \qquad E = X - KY \approx 0
$$

## 3. Gain Desensitization

open-loop $$A$$가 transistor parameter에 민감해도:
$$
A_{cl} = \frac{A}{1 + KA} \;\xrightarrow{KA \gg 1}\; \frac{1}{K}
$$
→ gain이 **passive component (resistor ratio)**로 결정. 감도:
$$
\frac{dA_{cl}/A_{cl}}{dA/A} = \frac{1}{1 + KA}
$$

## 4. Op-Amp Feedback

$$V_+ \approx V_-$$ (large loop gain):
$$
\text{non-inverting: } \frac{V_o}{V_s} = 1 + \frac{R_2}{R_1}, \qquad
\text{inverting: } \frac{V_o}{V_s} = -\frac{R_F}{R_{in}}
$$

## 5. CS with Degeneration = Local Feedback

$$
A \approx -g_m R_D \;(\text{open-loop}), \qquad
A_v = \frac{-g_m R_D}{1 + g_m R_S} \;\xrightarrow{g_m R_S \gg 1}\; -\frac{R_D}{R_S}
$$
gain이 $$g_m$$ 변화에 둔감 (source resistor $$R_S$$가 feedback factor).

## 6. Bandwidth Extension

single-pole open-loop $$A(s) = A_0/(1 + s/\omega_p)$$:
$$
A_{cl}(s) = \frac{A_0}{1 + KA_0 + s/\omega_p} \;\Rightarrow\;
\omega_{p,cl} = (1 + KA_0)\,\omega_p
$$
→ gain은 $$/(1+KA_0)$$, bandwidth는 $$\times(1+KA_0)$$ → **GBW ≈ 일정**.

## 7. Feedback Polarity 판단

1. input을 조금 증가
2. feedforward output 변화 관찰
3. feedback path가 input error를 **줄이면 negative**
4. error를 키우면 positive (불안정 가능)

---

# Part 2. I/O 저항 개선

## 8. 증폭기별 이상 저항

| 유형 | 입력 | 출력 | 이상 $$R_{in}$$ | 이상 $$R_{out}$$ |
|---|---|---|---:|---:|
| **Voltage** | V | V | ∞ | 0 |
| **Current** | I | I | 0 | ∞ |
| **Transconductance** | V | I | ∞ | ∞ |
| **Transimpedance** | I | V | 0 | 0 |

Negative feedback은 각 유형을 그 **이상 조건에 가깝게** 만든다. gain은 항상 loop gain만큼 감소.

## 9. Topology별 효과

| 유형 (mixing–sampling) | gain | $$R_{in}$$ | $$R_{out}$$ |
|---|---|---|---|
| **Voltage** (series–shunt) | $$\div(1+KA)$$ | $$\times(1+KA)$$ ↑ | $$\div(1+KA)$$ ↓ |
| **Transconductance** (series–series) | $$\div(1+KG_m)$$ | $$\times(1+KG_m)$$ ↑ | $$\times(1+KG_m)$$ ↑ |
| **Current** (shunt–series) | $$\div(1+KA_i)$$ | $$\div(1+KA_i)$$ ↓ | $$\times(1+KA_i)$$ ↑ |
| **Transimpedance** (shunt–shunt) | $$\div(1+KR_m)$$ | $$\div(1+KR_m)$$ ↓ | $$\div(1+KR_m)$$ ↓ |

**직관**
- **series mixing** (입력에서 전압 비교) → $$R_{in}$$ ↑ (test current에 input voltage 변화가 억제됨)
- **shunt mixing** (입력에서 전류 비교) → $$R_{in}$$ ↓
- **shunt sampling** (출력 전압 감지) → $$R_{out}$$ ↓ (output voltage 흔들림을 feedback이 되돌림)
- **series sampling** (출력 전류 감지) → $$R_{out}$$ ↑

암기보다 **이상적 증폭기 조건과 연결**해서 이해:
- voltage amp: $$R_{in}\uparrow$$, $$R_{out}\downarrow$$
- current amp: $$R_{in}\downarrow$$, $$R_{out}\uparrow$$
- transconductance amp: $$R_{in}\uparrow$$, $$R_{out}\uparrow$$
- transimpedance amp: $$R_{in}\downarrow$$, $$R_{out}\downarrow$$

## 10. 회로 예

- **Transconductance feedback** (diff amp + CS): output current를 feedback voltage로 변환해 입력에 되돌림 → $$G_m$$↓, $$R_{out}$$↑.
- **Current amplifier** (CG + CS): output current 일부를 current로 feedback → $$A_i$$↓, $$R_{in}$$↓, $$R_{out}$$↑.
- **TIA** (CG + CS + 큰 feedback resistor $$R_F$$):
$$
Z_{t,open} \approx -g_m R_D R_F, \qquad Z_{t,closed} \approx -R_F \;(\text{large loop gain})
$$
→ feedback이 충분히 크면 transimpedance gain이 $$R_F$$로 안정적으로 결정 (→ [15. 설계 프로젝트](15-design-project-tia-ctle.md)).

---

## 핵심 정리

- $$A_{cl} = A/(1+KA)$$; $$KA \gg 1$$이면 $$\approx 1/K$$ (resistor ratio). 감도 $$1/(1+KA)$$.
- Bandwidth $$\times(1+KA_0)$$, GBW ≈ 일정.
- Source degeneration = local negative feedback.
- Topology별: voltage(Rin↑,Rout↓) / current(Rin↓,Rout↑) / transconductance(Rin↑,Rout↑) / transimpedance(Rin↓,Rout↓). 개선비 = loop gain $$(1+KA)$$.
- TIA: $$Z_{t,closed} \approx -R_F$$.

## 복습 질문

- $$A_{cl} = A/(1+KA)$$에서 gain desensitization과 bandwidth extension을 각각 유도할 수 있나?
- series/shunt mixing이 $$R_{in}$$을, series/shunt sampling이 $$R_{out}$$을 각각 어느 방향으로 바꾸나?
- 네 증폭기 유형의 이상적 $$R_{in}$$·$$R_{out}$$과, feedback이 만드는 개선 방향을 연결할 수 있나?
- TIA에서 loop gain이 크면 $$Z_t \approx -R_F$$가 되는 이유는?
{% endraw %}

---

이전: [12. 필터](12-filters.md) · 다음: [14. 발진기 (Oscillators)](14-oscillators.md)
