---
layout: page
title: "05. Cascode 증폭기"
permalink: /studies/circuits/electric-circuits-2/05-cascode-amplifier/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `06 Cascode Amplifier` 보강

{% raw %}
## 개요

Cascode = **CS(하단) + CG(상단)**. CS가 transconductance를, CG가 current buffer로 output resistance를 크게 boost → 높은 voltage gain. 대가는 **headroom**(트랜지스터 stack).

---

## 1. Source Resistance가 있는 CG의 Output Resistance

CG stage의 source에 저항 $R_S$가 보이면 drain에서 본 저항이 커진다:
$$
R_{out} = R_D \parallel \big[\,r_o + R_S(1 + g_m r_o)\,\big]
$$
핵심: **source resistance가 $(1 + g_m r_o)$배로 증폭되어 drain에서 보인다.**

## 2. Cascode 구조

```text
CS(M1) + CG(M2)
```

| 소자 | 역할 |
|---|---|
| 하단 CS (M1) | 입력 전압 → drain 전류, $G_m \approx g_{m1}$ |
| 상단 CG (M2) | 전류를 출력 node로 전달, **output resistance boost** |

## 3. Output Resistance

$$
R_{out} \approx R_D \parallel \big[\,r_{o2} + r_{o1}(1 + g_{m2} r_{o2})\,\big]
\;\xrightarrow{g_{m2}r_{o2} \gg 1}\;
R_D \parallel (g_{m2}\, r_{o2}\, r_{o1})
$$
→ 단순 CS($r_o$)보다 **$\sim g_m r_o$배 큰** output resistance.

## 4. Voltage Gain

$$
A_v \approx -g_{m1} R_{out}
$$

**Telescopic cascode** (active load도 cascode로):
$$
R_{out,N} \approx g_{m2} r_{o2} r_{o1}, \qquad R_{out,P} \approx g_{m3} r_{o3} r_{o4}
$$
$$
A_v \approx -g_{m1}\,(R_{out,N} \parallel R_{out,P}) \sim -(g_m r_o)^2
$$

## 5. 장점 vs 단점

| 장점 | 단점 |
|---|---|
| 높은 output resistance | **headroom** — 여러 MOSFET이 supply 사이에 stack |
| 높은 voltage gain ($\sim (g_m r_o)^2$) | 각 소자가 saturation 유지할 최소 $V_{DS}$ 필요 |
| **Miller effect 감소** → bandwidth 개선 | low-voltage process에서 output swing 제한 |

(cascode가 M1의 drain 전압을 거의 고정 → M1의 $C_{gd}$가 큰 Miller 증배를 겪지 않음 → [10. 증폭기 주파수 응답](10-amplifier-frequency-response.md).)

---

## 핵심 정리

- Cascode = CS + CG. $R_{out} \approx g_{m2}r_{o2}r_{o1}$ ($\sim g_m r_o$배 boost).
- $A_v \approx -g_{m1}R_{out}$; telescopic이면 $\sim -(g_m r_o)^2$.
- 장점: 큰 gain·bandwidth (Miller↓). 단점: headroom·swing 제한.
- 상단 CG에서 source 저항 $R_S$는 drain에서 $R_S(1+g_m r_o)$로 보인다.

## 복습 질문

- cascode 출력저항 $g_{m2}r_{o2}r_{o1}$을 유도하고, "M1 drain 전압을 고정한다"는 직관과 연결할 수 있나?
- telescopic cascode의 $A_v \sim -(g_m r_o)^2$는 어디서 나오나?
- cascode의 headroom 문제가 low-voltage 공정에서 왜 심각한가?
{% endraw %}

---

이전: [04. Source Follower & Common-Gate](04-source-follower-and-common-gate-sf-cg.md) · 다음: [06. Bias & Current Mirrors](06-bias-circuits-and-current-mirrors.md)
