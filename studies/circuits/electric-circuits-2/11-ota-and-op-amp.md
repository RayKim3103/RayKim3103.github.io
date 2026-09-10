---
layout: page
title: "11. OTA & Op-Amp"
permalink: /studies/circuits/electric-circuits-2/11-ota-and-op-amp/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `15 OTA and Op-Amp` 보강

{% raw %}
## 개요

**OTA** = differential input을 output current로 바꾸는 operational transconductance amplifier. current mirror + single-ended differential amplifier + CS load로 구성. voltage amplifier가 필요하면 **source follower buffer**를 붙여 op-amp로 확장. op-amp는 보통 **negative feedback**과 함께 써서 gain을 resistor ratio로 안정화.

---

## 1. OTA의 구성

| 요소 | 역할 |
|---|---|
| current mirrors | bias |
| single-ended differential amplifier | 1단 gain |
| CS stage with PMOS load | 2단 gain |

```text
input: voltage,  output: current  →  transconductance amplifier
```

## 2. Input Common-Mode Range (ICMR)

입력단 MOSFET이 모두 saturation을 유지해야:
$$
V_{CM,min} = V_{GS3} - |V_{TH,p}|, \qquad V_{CM,max} = V_{DD} - V_{SG5} - V_{SG1} + |V_{TH,p}|
$$
큰 ICMR → bias current를 작게 해 필요한 overdrive를 줄임. 단 current를 너무 줄이면 $$g_m$$·bandwidth 감소.

## 3. Transconductance / Voltage Gain

2단(differential pair + gain stage) 구조:
$$
G_{m,total} \approx -g_{m1}g_{m6}(r_{o2} \parallel r_{o4}), \qquad R_{out} \approx r_{o6} \parallel r_{o7}
$$
$$
A_v \approx -g_{m1}g_{m6}(r_{o2} \parallel r_{o4})(r_{o6} \parallel r_{o7})
$$

## 4. Frequency Response

각 stage의 dominant pole은 주로 **Miller capacitance**:
$$
C_1 \approx g_{m2}(r_{o2} \parallel r_{o4})\,C_{gd2} \quad(\text{1단})
$$
$$
C_2 \approx g_{m6}(r_{o6} \parallel r_{o7})\,C_{gd6} \quad(\text{2단})
$$
2단 output resistance가 source resistance보다 훨씬 크면 **2번째 pole이 전체 dominant pole**이 될 수 있다. (2단 op-amp는 Miller compensation으로 dominant pole을 분리하고 pole splitting을 이용.)

## 5. OTA → Op-Amp

OTA는 output resistance가 크다 → voltage amplifier로 쓰려면 **output buffer** 필요:
```text
OTA + source follower  →  op-amp
```
**Ideal op-amp**: $$R_{in} = \infty$$, $$R_{out} = 0$$, open-loop gain 매우 큼.

## 6. Negative Feedback Op-Amp

op-amp는 거의 항상 negative feedback과 함께.

**Non-inverting** (ideal op-amp $$V_+ = V_-$$):
$$
\frac{V_o}{V_s} = 1 + \frac{R_2}{R_1}
$$
**Inverting**:
$$
\frac{V_o}{V_s} = -\frac{R_F}{R_{in}}
$$

| 장점 | |
|---|---|
| gain이 transistor parameter가 아니라 **resistor ratio**로 결정 | gain 변경 쉬움 |
| 안정성 향상 | $$V_+ \approx V_-$$ (loop gain 큼) |

---

## 핵심 정리

- OTA = transconductance amplifier (V in → I out), current mirror + diff pair + CS load.
- ICMR은 입력 pair·current source·load의 saturation 조건으로 결정.
- OTA voltage gain $$\approx G_m R_{out}$$ (2단이면 $$g_{m1}g_{m6}$$ × 저항곱).
- Op-amp = OTA + voltage buffer(SF). Ideal: $$R_{in}=\infty$$, $$R_{out}=0$$, $$A\to\infty$$.
- Negative feedback: gain = $$1+R_2/R_1$$ (non-inv) 또는 $$-R_F/R_{in}$$ (inv), $$V_+ \approx V_-$$.

## 복습 질문

- OTA와 op-amp의 차이(무엇을 붙이면 op-amp가 되나)?
- ICMR의 상·하한이 어떤 소자의 saturation 조건으로 정해지나?
- 2단 OTA에서 dominant pole이 2번째 stage에 생길 수 있는 이유는?
- negative feedback에서 $$V_+ \approx V_-$$가 성립하는 이유를 loop gain으로 설명할 수 있나?
{% endraw %}

---

이전: [10. 증폭기 주파수 응답](10-amplifier-frequency-response.md) · 다음: [12. 필터](12-filters.md)
