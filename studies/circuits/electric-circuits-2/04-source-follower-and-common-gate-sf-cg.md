---
layout: page
title: "04. Source Follower & Common-Gate (SF · CG)"
permalink: /studies/circuits/electric-circuits-2/04-source-follower-and-common-gate-sf-cg/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `05 Source Follower and Common-Gate` 보강

{% raw %}
## 개요

**Source follower(SF)** = voltage gain이 1보다 약간 작은 voltage buffer. **Common-gate(CG)** = 낮은 $$R_{in}$$·높은 $$R_{out}$$의 current buffer. CS 뒤에 SF를 붙이면 작은 load에서도 CS gain을 보존.

---

## 1. Source Follower (Common-Drain)

$$r_o$$ 무시:
$$
A_v = \frac{g_m R_L}{1 + g_m R_L} \;\lesssim 1
$$
$$
R_{in} = \infty, \qquad R_{out} \approx \frac{1}{g_m} \parallel R_L
$$

특징: gain ≈ 1 (1보다 작음), $$R_{in}$$ 큼, $$R_{out}$$ 작음 → **voltage buffer**.

### $$r_o$$·body effect 포함
$$
R_{out} \approx \frac{1}{g_m} \parallel r_o \parallel R_L
$$
body effect까지 고려하면 effective transconductance가 $$g_m + g_{mb}$$가 되어 $$R_{out}$$이 더 작아진다:
$$
R_{out} \approx \frac{1}{g_m + g_{mb}} \parallel r_o \parallel R_L
$$

## 2. CS + SF

CS 출력에 작은 $$R_L$$을 직접 연결하면
$$
A_v = -g_m(R_D \parallel R_L)
$$
로 gain이 크게 줄어든다. SF를 buffer로 삽입하면:
- CS는 SF의 큰 $$R_{in}$$을 부하로 봐서 **gain 유지** ($$-g_m(R_D \parallel r_o)$$)
- SF가 작은 $$R_{out}$$으로 $$R_L$$을 구동

```text
CS: large gain    →    SF: voltage buffer    →    small RL
```

## 3. Common-Gate Amplifier

gate = AC ground, 입력 = source, 출력 = drain.

$$r_o$$ 무시:
$$
R_{in} = \frac{1}{g_m}, \qquad R_{out} = R_D, \qquad A_v = g_m R_D \;(\text{non-inverting})
$$

### CG as Current Buffer
$$R_{in}$$ 작고 $$R_{out}$$ 큼 → current amplifier/buffer. 이상적 current gain
$$
A_i \approx -1
$$
(source로 들어온 전류가 거의 그대로 drain으로).

### $$r_o$$ 포함 CG (강의 homework 결과)
$$
R_{in} = \frac{r_o + R_D}{1 + g_m r_o}, \qquad R_{out} = r_o \parallel R_D, \qquad A_v = \frac{(1 + g_m r_o)R_D}{R_D + r_o}
$$
$$g_m r_o \gg 1$$이면 $$R_{in} \to 1/g_m$$.

## 4. CS · SF · CG 비교

| 회로 | gain | $$R_{in}$$ | $$R_{out}$$ | 대표 용도 |
|---|---:|---:|---:|---|
| **CS** | 큼, negative | 큼 (∞) | 중간/큼 | transconductance, 전압 이득 |
| **SF** | ≈ 1 | 큼 (∞) | 작음 ($$1/g_m$$) | voltage buffer |
| **CG** | positive, $$g_m R_D$$ | 작음 ($$1/g_m$$) | 큼 | current buffer, wideband 입력 |

---

## 핵심 정리

- SF: $$A_v = g_m R_L/(1+g_m R_L) \lesssim 1$$, $$R_{out} \approx 1/(g_m+g_{mb}) \parallel r_o \parallel R_L$$. voltage buffer.
- CG: $$R_{in} = 1/g_m$$, $$A_v = g_m R_D$$ (non-inverting), $$A_i \approx -1$$. current buffer.
- CS+SF: SF가 CS의 load effect를 흡수해 CS gain을 보존하면서 작은 $$R_L$$을 구동.

## 복습 질문

- SF의 gain 식과 buffer 역할, body effect가 $$R_{out}$$을 더 낮추는 이유는?
- CG의 $$R_{in} \approx 1/g_m$$이 낮은 이유, current gain이 $$\approx -1$$인 이유는?
- CS+SF 조합이 load effect를 줄이는 메커니즘은?
{% endraw %}

---

이전: [03. Common-Source 증폭기](03-common-source-amplifier-cs.md) · 다음: [05. Cascode 증폭기](05-cascode-amplifier.md)
