---
layout: page
title: "03. BJT 기본 동작"
permalink: /studies/circuits/electric-circuits-1/03-bjt-operation/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/03%20BJT%20%EA%B8%B0%EB%B3%B8%20%EB%8F%99%EC%9E%91.md) · 교재: Razavi Ch.4

{% raw %}
## 한눈에 보기

BJT를 **전압제어 전류원(VCCS)**으로 이해한다. transconductance, forward active 동작, $$I_C$$, $$\beta$$, hybrid-π 소신호 모델, Early effect, 동작 영역, PNP.

```text
좋은 전압원/전류원 → VCCS → BJT 구조 → collector current → β
→ small signal model (gm, rπ, ro) → Early effect → operation region → PNP
```

---

## 1. 증폭기의 기본 아이디어

전압 이득 $$A_v = v_{out}/v_{in}$$. BJT 증폭기: **입력 전압 변화 → 출력 전류 변화 → 부하저항 통과 → 출력 전압**.

```text
v_in → i_out = gm·v_in → i_out·R → v_out
```

좋은 증폭기 조건:
- 입력 전압 변화 대비 출력 전류 변화($$g_m$$)가 커야
- 출력단 부하/출력저항이 적절히 커야
- **DC bias**로 트랜지스터를 원하는 동작점(Q point)에

### 좋은 전압원 / 좋은 전류원
| | 조건 |
|---|---|
| 좋은 전압원 | 부하 변해도 전압 유지 → **출력저항 작아야** |
| 좋은 전류원 | 부하 변해도 전류 유지 → **출력저항 커야** |

트랜지스터는 특정 영역에서 VCCS처럼 동작 → $$i_{out} = g_m v_{in}$$, $$v_{out} = i_{out}R$$ → 이득 크게 하려면 $$g_m \uparrow$$, $$R \uparrow$$.

---

## 2. BJT 구조와 forward active

npn = emitter / base / collector.
- **emitter**: 강하게 도핑, 캐리어 방출
- **base**: 매우 얇고 약하게 도핑된 제어 영역
- **collector**: 약하게 도핑, 캐리어 수집

**Forward active** 조건:
- base–emitter 접합: **순방향** ($$V_{BE} > 0$$, npn)
- base–collector 접합: **역방향** ($$V_{BC} < 0$$, 즉 $$V_C > V_B$$)

npn 기준 $$V_B > V_E$$ 그리고 $$V_C > V_B$$.

물리: emitter가 base로 전자를 injection → 얇은 base를 대부분 통과(재결합 소량) → collector가 수집. base 전류는 (a) 재결합분 (b) base→emitter 정공 injection.

## 3. Collector Current

$$
I_C = I_S\,e^{V_{BE}/V_T} \qquad (\text{정확히는 } I_S(e^{V_{BE}/V_T}-1),\ \text{forward에서 }-1\ \text{무시})
$$

$$V_{BE}$$가 지수적으로 $$I_C$$를 제어 → BJT = VCCS의 핵심. $$I_C$$를 10배 늘리려면 $$\Delta V_{BE} = V_T\ln10 \approx 60\,\text{mV}$$. $$\partial V_{BE}/\partial T \approx -2\,\text{mV/°C}$$ (일정 $$I_C$$).

## 4. 전류 이득 β, α

$$
I_C = \beta I_B, \qquad I_B = \frac{I_C}{\beta}, \qquad I_E = I_C + I_B = (\beta+1)I_B
$$
$$
\alpha = \frac{I_C}{I_E} = \frac{\beta}{\beta+1}, \qquad \beta = \frac{\alpha}{1-\alpha}
$$

$$\beta$$(보통 50~300)는 **공정·도핑·온도·$$I_C$$에 크게 의존** → 정확한 상수로 믿지 말 것. 설계는 $$\beta$$에 둔감하게(bias 안정화, degeneration).

## 5. 동작점 예시

$$I_S = 5\times10^{-19}\,\text{A}$$, $$V_{BE} = 800\,\text{mV}$$, $$\beta = 100$$, $$V_T = 26\,\text{mV}$$:

1. $$I_C = I_S\,e^{0.8/0.026} = 5\times10^{-19}\times e^{30.8} \approx 5\times10^{-19}\times2.4\times10^{13} \approx 1.2\times10^{-5}\,\text{A}$$ ($$\approx$$ 1.2 mA 스케일; 정확값은 계산기로)
2. $$I_B = I_C/100$$
3. $$I_E = I_C + I_B$$
4. collector에 $$R_C$$ 있으면 $$V_C = V_{CC} - I_C R_C$$ → $$V_C > V_B$$ 확인해 active 검증.

---

## 6. Transconductance

$$
g_m = \frac{\partial I_C}{\partial V_{BE}} = \frac{I_S e^{V_{BE}/V_T}}{V_T} = \boxed{\frac{I_C}{V_T}}
$$

상온: $$g_m \approx I_C/(26\,\text{mV})$$ → 예: $$I_C = 1\,\text{mA}$$ → $$g_m \approx 38.5\,\text{mS}$$ ($$1/g_m \approx 26\,\Omega$$).
**DC 동작점 $$I_C$$가 정해지면 $$g_m$$이 정해진다** — MOSFET과 달리 소자 크기와 무관.

## 7. Hybrid-π 소신호 모델

| 요소 | 값 | 의미 |
|---|---|---|
| $$r_\pi$$ | $$\beta/g_m = \beta V_T/I_C = V_T/I_B$$ | base–emitter 입력저항 |
| $$g_m v_\pi$$ | 제어 전류원 | collector–emitter 사이 |
| $$r_o$$ | $$V_A/I_C$$ | Early effect 출력저항 |

기본 관계: $$i_c = g_m v_\pi$$, $$v_\pi = i_b r_\pi$$, $$\;g_m r_\pi = \beta$$.

**Large signal** ($$I_C = I_S e^{V_{BE}/V_T}$$): DC 동작점·큰 변화.
**Small signal**: 동작점 주변 선형화, 작은 AC 이득 계산.
```text
DC bias로 Q point → Q point에서 gm, rπ, ro → AC 등가회로 해석
```

## 8. Early Effect

이상적 전류원은 $$V_{CE}$$ 변해도 $$I_C$$ 일정. 실제로는 $$V_{CE} \uparrow$$ → 공핍층이 base로 확장 → **effective base width 감소**(base-width modulation) → $$I_C$$ 소폭 증가.

$$
I_C = I_S e^{V_{BE}/V_T}\left(1 + \frac{V_{CE}}{V_A}\right), \qquad r_o = \frac{\partial V_{CE}}{\partial I_C} \approx \frac{V_A}{I_C}
$$

Early voltage $$V_A$$ (보통 20~100 V)가 클수록 $$r_o$$ ↑ → 더 이상적인 전류원. **Intrinsic gain** $$g_m r_o = V_A/V_T$$ — $$I_C$$와 무관한 소자 고유 상한 (BJT는 보통 1000~4000).

## 9. 소신호 파라미터 요약

DC 동작점의 $$I_C$$만 알면:
$$
g_m = \frac{I_C}{V_T}, \qquad r_\pi = \frac{\beta}{g_m}, \qquad r_o = \frac{V_A}{I_C}
$$
→ 이후 [BJT 증폭기](04-bjt-amplifiers.md) 해석의 출발점.

---

## 10. 동작 영역

| 영역 | B–E 접합 | B–C 접합 | 동작 |
|---|---|---|---|
| **Cutoff** | 역/무바이어스 | 역 | off, $$I_C \approx 0$$ |
| **Forward active** | 순방향 | 역방향 | **VCCS** (증폭) |
| **Saturation** | 순방향 | 순방향 | $$V_{CE}$$ 작음, $$I_C$$가 $$V_{CE}$$에 의존 (스위치 on) |
| **Reverse active** | 역 | 순 | 거의 사용 안 함, β 매우 작음 |

- **Edge of active**: $$V_{BC} = 0$$ ($$V_{CE} = V_{BE} \approx 0.8\,\text{V}$$) 근처가 경계. $$V_{CE}$$가 더 낮아지면 soft → hard saturation.
- Saturation에서 $$V_{CE,sat} \approx 0.1\text{–}0.2\,\text{V}$$, $$\beta_{forced} = I_C/I_B < \beta$$.

## 11. PNP Transistor

NPN의 complementary. 전류 방향·전압 극성이 모두 반대: emitter가 최고 전위, $$V_{EB} > 0$$로 켜짐, $$I_C$$는 collector에서 나옴. 소신호 모델의 **형태**는 npn과 동일($$g_m, r_\pi, r_o$$ 식 같음) — 기준 전압·전류 방향만 주의.

## 12. BJT 해석 절차

1. DC bias 해석
2. $$V_{BE}$$ 또는 회로 방정식으로 $$I_C$$
3. $$V_C, V_B, V_E$$ 계산 → active region 확인
4. $$g_m, r_\pi, r_o$$ 계산
5. AC 소신호 등가회로로 치환 (DC원 → ground, 큰 커패시터 → short)
6. $$v_{out}/v_{in}$$, $$R_{in}$$, $$R_{out}$$

---

## 핵심 정리

- BJT는 forward active에서 VCCS: $$I_C = I_S e^{V_{BE}/V_T}$$, 10× 전류 = +60 mV.
- $$\beta = I_C/I_B$$, $$\alpha = \beta/(\beta+1)$$, $$I_E = (\beta+1)I_B$$. $$\beta$$는 불안정한 파라미터.
- $$g_m = I_C/V_T$$ (동작점만으로 결정), $$r_\pi = \beta/g_m$$, $$r_o = V_A/I_C$$.
- Early effect = base-width modulation → 유한 $$r_o$$, intrinsic gain $$g_m r_o = V_A/V_T$$.
- 4영역: cutoff / forward active / saturation / reverse active. 증폭은 forward active.

## 복습 질문

- forward active의 두 접합 바이어스 조건, npn에서 $$V_B, V_C, V_E$$ 부등식은?
- $$g_m = I_C/V_T$$가 소자 크기와 무관한 이유, MOSFET과의 차이는?
- $$r_\pi, r_o$$를 $$I_C, \beta, V_A, V_T$$로 표현하고, intrinsic gain $$g_m r_o$$가 $$V_A/V_T$$인 것을 보일 수 있나?
- Early effect의 물리적 원인(base-width modulation)을 설명할 수 있나?
- saturation과 forward active의 경계($$V_{BC}=0$$)와, saturation에서 $$\beta_{forced}$$의 의미는?
{% endraw %}

---

이전: [02. 다이오드 응용](02-diode-applications.md) · 다음: [04. BJT 증폭기](04-bjt-amplifiers.md)
