---
layout: page
title: "05. MOSFET 기본 동작"
permalink: /studies/circuits/electric-circuits-1/05-mosfet-operation/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/05%20MOSFET%20%EA%B8%B0%EB%B3%B8%20%EB%8F%99%EC%9E%91.md) · 교재: Razavi Ch.6

{% raw %}
## 한눈에 보기

MOSFET 구조와 전류식. BJT의 한계였던 **입력 전류 문제를 제거**하고, gate 전기장으로 channel을 형성해 drain current를 제어.

```text
MOS 구조 → electric field → inversion → channel
→ triode region → saturation region → gm → λ effect → ro → PMOS
```

---

## 1. 구조

**M**etal–**O**xide–**S**emiconductor **F**ield **E**ffect **T**ransistor.

| 단자 | 설명 |
|---|---|
| **gate** | 금속/폴리실리콘 전극 |
| **oxide** | $$SiO_2$$ 절연층 (두께 $$t_{ox}$$) |
| **body/substrate** | p-substrate (NMOS) |
| **source** | 캐리어가 들어오는 쪽 (NMOS: 낮은 전위) |
| **drain** | 캐리어가 빠져나가는 쪽 |

gate–channel 사이 oxide → **DC gate current ≈ 0** (BJT의 base current와 결정적 차이). 단위면적 gate 커패시턴스 $$C_{ox} = \varepsilon_{ox}/t_{ox}$$.

## 2. NMOS와 inversion

p-substrate 위 n+ source / n+ drain. $$V_{GS}$$ 증가 → gate 아래 정공이 밀려나고(depletion) → 더 증가하면 전자가 모여 **n-channel 형성(strong inversion)**.

$$
V_{GS} < V_{th}: \text{ off (또는 weak inversion, subthreshold)} \\
V_{GS} > V_{th}: \text{ strong inversion, channel 존재}
$$

$$V_{th}$$ = threshold voltage (공정 상수, body effect로 $$V_{SB}$$에 의존).

### Field effect
$$
V_G \uparrow \;\Rightarrow\; Q_{ch} \uparrow \;\Rightarrow\; I_D \uparrow
$$
→ MOSFET도 BJT처럼 **전압제어 전류원**.

---

## 3. 동작 영역

overdrive voltage: $$V_{OV} = V_{GS} - V_{th}$$ (= $$V_{GS,eff}$$).

| 영역 | 조건 | 동작 |
|---|---|---|
| **Cutoff** | $$V_{GS} < V_{th}$$ | channel 없음, $$I_D \approx 0$$ |
| **Triode (linear)** | $$V_{GS} > V_{th}$$, $$V_{DS} < V_{OV}$$ | channel이 S→D 연결, **전압제어 가변저항** |
| **Pinch-off (경계)** | $$V_{DS} = V_{OV}$$ | drain 쪽 channel이 막히기 시작 |
| **Saturation** | $$V_{GS} > V_{th}$$, $$V_{DS} \ge V_{OV}$$ | $$I_D$$가 $$V_{DS}$$에 거의 무관 → **전류원** (증폭기용) |

## 4. 전류식

### Triode
$$
I_D = \mu_n C_{ox}\frac{W}{L}\left[(V_{GS}-V_{th})V_{DS} - \frac{V_{DS}^2}{2}\right]
$$
$$V_{DS} \ll V_{OV}$$ 이면
$$
I_D \approx \mu_n C_{ox}\frac{W}{L}(V_{GS}-V_{th})V_{DS}
\;\Rightarrow\; R_{on} = \frac{1}{\mu_n C_{ox}\frac{W}{L}V_{OV}}
$$

### Saturation (square-law)
$$
\boxed{I_D = \frac{1}{2}\mu_n C_{ox}\frac{W}{L}(V_{GS}-V_{th})^2}
$$

$$I_D \propto V_{OV}^2$$ → **BJT의 지수식과 결정적 차이**. Triode 식에 $$V_{DS} = V_{OV}$$를 넣으면 saturation 식과 연속으로 이어진다.

> deep-submicron 소자는 velocity saturation으로 $$I_D \propto V_{OV}$$ 에 가까워짐 (square-law는 이상적 long-channel).

---

## 5. Transconductance

$$
g_m = \frac{\partial I_D}{\partial V_{GS}}\bigg|_{sat}
$$

세 가지 등가 표현 (동작점에 따라 골라 씀):

$$
g_m = \mu_n C_{ox}\frac{W}{L}V_{OV}
= \sqrt{2\mu_n C_{ox}\frac{W}{L}I_D}
= \frac{2I_D}{V_{OV}}
$$

BJT와 비교: 같은 전류에서 **MOSFET $$g_m$$이 훨씬 작다** ($$2I_D/V_{OV}$$ vs $$I_C/V_T$$, $$V_{OV}$$는 보통 0.2~0.4 V로 $$V_T$$=26 mV보다 큼). $$g_m$$을 키우려면 $$I_D$$↑ 또는 $$W/L$$↑ (같은 $$I_D$$면 $$V_{OV}$$↓).

## 6. 소신호 모델

- gate 절연 → **DC 입력저항 매우 큼** ($$r_{gs} \to \infty$$)
- drain–source 제어 전류원 $$g_m v_{gs}$$
- channel length modulation → $$r_o$$ 병렬

$$
i_d = g_m v_{gs} + \frac{v_{ds}}{r_o}
$$

(body를 소신호로 다루면 $$g_{mb}v_{bs}$$ 항 추가, $$g_{mb} = \eta g_m$$, $$\eta \approx 0.1\text{–}0.3$$.)

## 7. Channel Length Modulation (λ effect)

$$V_{DS} \uparrow$$ → pinch-off point가 source 쪽으로 이동 → **effective $$L$$ 감소** → saturation에서도 $$I_D$$ 소폭 증가.

$$
I_D = \frac{1}{2}\mu_n C_{ox}\frac{W}{L}(V_{GS}-V_{th})^2(1 + \lambda V_{DS})
$$
$$
r_o = \frac{\partial V_{DS}}{\partial I_D} \approx \frac{1}{\lambda I_D} \qquad (\lambda \propto 1/L)
$$

$$\lambda$$ 작을수록(= **long channel**) $$r_o$$ ↑ → 좋은 전류원. Intrinsic gain
$$
g_m r_o = \frac{2I_D}{V_{OV}}\cdot\frac{1}{\lambda I_D} = \frac{2}{\lambda V_{OV}}
$$
→ $$I_D$$와 무관, MOSFET는 보통 20~100 (BJT보다 작음).

## 8. BJT vs MOSFET

| 항목 | BJT | MOSFET |
|---|---|---|
| 제어 변수 | $$V_{BE}$$ | $$V_{GS}$$ |
| 전류식 | 지수 $$I_S e^{V_{BE}/V_T}$$ | 제곱 $$\frac12\mu C_{ox}\frac WL V_{OV}^2$$ |
| 입력 DC 전류 | $$I_B = I_C/\beta$$ 존재 | ≈ 0 (gate 절연) |
| $$g_m$$ | $$I_C/V_T$$ (크다, 크기 무관) | $$2I_D/V_{OV}$$ (작다, $$W/L$$ 의존) |
| 출력저항 원인 | Early effect, $$r_o = V_A/I_C$$ | λ effect, $$r_o = 1/(\lambda I_D)$$ |
| intrinsic gain | $$V_A/V_T$$ (~1000s) | $$2/(\lambda V_{OV})$$ (~10s) |

## 9. PMOS

NMOS의 complementary. n-well 위 p+ source/drain, **정공 channel**. 극성 반대:
- source가 높은 전위 쪽
- $$V_{GS} < V_{th,p}$$ ($$V_{th,p} < 0$$), 즉 gate가 source보다 충분히 낮으면 켜짐
- $$\mu_p < \mu_n$$ (약 1/2~1/3) → 같은 전류엔 $$W$$를 2~3배
- CMOS에서 NMOS와 complementary 구조 ([08. CMOS 인버터](08-cmos-inverter.md))

---

## 핵심 정리

- MOSFET = gate 전기장으로 channel 제어, DC gate current ≈ 0.
- NMOS: $$V_{GS} > V_{th}$$에서 channel. Triode($$V_{DS}<V_{OV}$$) = 가변저항, Saturation($$V_{DS}\ge V_{OV}$$) = 전류원.
- Saturation $$I_D = \frac12\mu_n C_{ox}\frac WL V_{OV}^2 \propto V_{OV}^2$$.
- $$g_m = \mu_n C_{ox}\frac WL V_{OV} = \sqrt{2\mu_n C_{ox}\frac WL I_D} = 2I_D/V_{OV}$$.
- λ effect: $$r_o = 1/(\lambda I_D)$$, $$\lambda \propto 1/L$$; intrinsic gain $$g_m r_o = 2/(\lambda V_{OV})$$.
- PMOS = NMOS complementary, 극성 반대, $$\mu_p < \mu_n$$.

## 복습 질문

- cutoff / triode / saturation 조건을 $$V_{GS}, V_{DS}, V_{th}, V_{OV}$$로 쓸 수 있나? pinch-off의 의미는?
- saturation $$I_D$$ 식과 triode $$I_D$$ 식이 $$V_{DS}=V_{OV}$$에서 연속임을 보일 수 있나?
- $$g_m$$의 세 가지 표현을 유도하고, 같은 $$I_D$$에서 BJT보다 작은 이유는?
- λ effect의 물리적 원인과 $$r_o = 1/(\lambda I_D)$$, long channel이 유리한 이유는?
- BJT와 MOSFET의 제어변수·전류식·$$g_m$$·intrinsic gain 차이를 표로 재현할 수 있나?
{% endraw %}

---

이전: [04. BJT 증폭기](04-bjt-amplifiers.md) · 다음: [06. MOSFET 증폭기](06-mosfet-amplifiers.md)
