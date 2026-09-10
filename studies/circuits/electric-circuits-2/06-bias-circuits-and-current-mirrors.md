---
layout: page
title: "06. Bias 회로와 Current Mirror"
permalink: /studies/circuits/electric-circuits-2/06-bias-circuits-and-current-mirrors/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `07 Bias Circuits and Current Mirrors` 보강

{% raw %}
## 개요

MOS amplifier의 small-signal parameter($$g_m$$, $$r_o$$)는 **bias current와 $$V_{GS}$$**로 결정된다. 원하는 동작점을 만들고 유지하는 bias 회로가 필수. IC에서는 저항보다 **current mirror**로 기준전류 $$I_{REF}$$를 복사·scaling한다.

---

## 1. 왜 Bias가 필요한가

MOSFET이 saturation에 있고 $$I_D$$, $$V_{GS}$$, $$g_m$$, $$r_o$$가 적절해야 원하는 gain:
$$
g_m = \frac{2I_D}{V_{OV}}, \qquad r_o \approx \frac{1}{\lambda I_D}
$$
→ **DC operating point를 먼저 정한 뒤 small-signal을 얹는다.**

## 2. Gate Biasing

저항 divider:
$$
V_{GS} = V_{DD}\cdot\frac{R_2}{R_1 + R_2}
$$
gate DC current ≈ 0이라 BJT보다 divider 설계가 단순 (아주 큰 저항 가능). 입력 신호는 **coupling capacitor**로.

## 3. Coupling Capacitor

$$
Z_C = \frac{1}{j\omega C}
$$
- DC: **open** (bias 분리)
- 관심 주파수에서 $$\omega C$$ 충분히 크면: **short** (AC 통과)

→ DC bias 유지 + AC input만 전달. 단, coupling network가 $$R_{in}$$을 낮추고 **low-frequency (high-pass) pole**을 만든다 → [10. 주파수 응답](10-amplifier-frequency-response.md).

## 4. Current Mirror

diode-connected 기준 소자에 $$I_{REF}$$를 흘리면 그에 맞는 $$V_{GS}$$가 정해지고, 이를 복사 소자와 공유:
$$
I_{copy} = I_{REF}\cdot\frac{(W/L)_{copy}}{(W/L)_{ref}}
$$
전제: 두 MOSFET saturation, $$V_{TH}$$·process matching, channel-length modulation 작음.

**장점**: 하나의 $$I_{REF}$$로 여러 bias current, size ratio로 scaling, IC에서 저항보다 면적·정밀도 유리.

## 5. Current Mirror의 제한

### Output Compliance
복사 소자가 saturation을 유지해야 함 (NMOS current sink):
$$
V_O \ge V_{GS} - V_{TH} = V_{OV}
$$

### Channel-Length Modulation
$$V_O$$가 변하면 $$V_{DS}$$가 변하고, finite $$r_o$$ 때문에 $$I_O \ne I_{REF}$$:
$$
I_O \approx I_{REF} + \frac{V_O - V_{GS}}{r_o}
$$
→ ideal이 아니라 **유한 output resistance $$r_o$$를 가진 current source**. cascode mirror로 개선.

## 6. CS with Current-Mirror / Current-Source Load

저항 load 대신:
- DC bias current를 안정적으로 설정
- load resistance를 크게 만들어 **gain 증가** ($$A_v = -g_m(r_{o1}\parallel r_{o2})$$)
- IC 면적 감소

단, current mirror의 left side가 input resistance처럼 작용하거나 output voltage range가 제한될 수 있음.

---

## 핵심 정리

- small-signal parameter는 bias에 의존 → DC 동작점 먼저.
- Coupling capacitor: DC open, AC short; low-frequency pole 생성.
- Current mirror: $$I_{copy}/I_{REF} = (W/L)$$ 비. 소자 matching 기반, IC-friendly.
- 오차 원인: output compliance ($$V_O \ge V_{OV}$$) + channel-length modulation ($$I_O \approx I_{REF} + (V_O-V_{GS})/r_o$$).
- Current-source load → 큰 $$R_{out}$$으로 gain↑.

## 복습 질문

- $$g_m$$, $$r_o$$가 bias current에 어떻게 의존하는가?
- coupling capacitor가 만드는 pole의 종류(high-pass)와, 충분히 크게 잡는 조건은?
- current mirror의 전류비가 $$(W/L)$$ 비인 이유와, 실제 오차의 두 원인은?
{% endraw %}

---

이전: [05. Cascode 증폭기](05-cascode-amplifier.md) · 다음: [07. Differential Amplifiers](07-differential-amplifiers.md)
