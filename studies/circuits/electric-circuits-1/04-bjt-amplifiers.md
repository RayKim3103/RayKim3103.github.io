---
layout: page
title: "04. BJT 증폭기"
permalink: /studies/circuits/electric-circuits-1/04-bjt-amplifiers/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/04%20BJT%20%EC%A6%9D%ED%8F%AD%EA%B8%B0.md) · 교재: Razavi Ch.5

{% raw %}
## 한눈에 보기

BJT 증폭기 설계·해석: 임피던스 개념 → DC bias → 소신호 파라미터 → AC 등가회로 → **CE / CB / CC** 3구성 → emitter degeneration → 다단 이득.

```text
임피던스 → DC bias → gm, rπ, ro → AC equivalent
→ CE/CB/CC → emitter degeneration → multi-stage gain
```

---

## 1. 임피던스가 중요한 이유

증폭기는 앞단·뒷단에 연결되어 쓰인다 → 이득만이 아니라 **입력저항·출력저항**을 함께 봐야 한다.

| | 전압 증폭기 (이상) | 전류 증폭기 (이상) |
|---|---|---|
| 입력저항 | **커야** (입력 전압 덜 깎임) | 작아야 (입력 전류 잘 받음) |
| 출력저항 | **작아야** (부하에 전압 전달) | 커야 (부하 변해도 전류 유지) |

**입력 loading**: 소스 내부저항 $R_S$, 증폭기 입력저항 $R_{in}$ →
$$
v_1 = \frac{R_{in}}{R_S + R_{in}}\,v_{in}
$$
**출력 loading**: 증폭기 출력저항 $R_{amp}$, 부하 $R_L$ →
$$
v_{out} = \frac{R_L}{R_{amp} + R_L}\,v_{amp}
$$

## 2. BJT 단자별 임피던스

```text
collector impedance  >  base impedance  >  emitter impedance
     (~ ro)                 (~ rπ)            (~ 1/gm)
```

| 들여다보는 단자 | 저항 | 조건 |
|---|---|---|
| **base** | $r_\pi$ (emitter AC ground) | emitter에 $R_E$ 있으면 $r_\pi + (\beta+1)R_E$ |
| **collector** | $r_o$ (base·emitter AC ground) | 회로에 $R_C$ 있으면 $R_C \parallel r_o$ |
| **emitter** | $\approx 1/g_m$ (base AC ground) | 정확히는 $(r_\pi + R_B)/(\beta+1) \parallel r_o$ |

### Reduction theorem (임피던스 반사)
- emitter의 $R_E$ → base에서 보면 $\times(\beta+1)$ → $(\beta+1)R_E$
- base의 $R_B$ → emitter에서 보면 $\div(\beta+1)$ → $R_B/(\beta+1)$

→ KCL을 매번 안 써도 임피던스를 빠르게 계산.

---

## 3. DC Bias

작은 AC 신호를 **선형** 증폭하려면 Q point가 필요. bias 없으면 신호가 비선형 구간을 오가며 왜곡(clipping).

**해석 규칙**
- DC 해석: 커패시터 = **open**
- AC 소신호 해석: 큰 coupling capacitor = **short**, DC 전원($V_{CC}$) = **AC ground**

### Resistive divider bias
$$
V_B = \frac{R_2}{R_1 + R_2}V_{CC} \qquad (\text{조건: 분압 전류 } I_X \gg I_B)
$$
$I_X \gg I_B$ 여야 base 전류가 분압점을 흔들지 않아 $\beta$에 둔감. 그다음 $V_E = V_B - V_{BE}$, $I_C \approx I_E = V_E/R_E$.

### Self bias (collector–base 저항 $R_B$)
출력 전압 변화가 base bias로 되먹임 → 동작점 안정화 (음의 피드백).
$$
V_{CC} = I_C R_C + I_B R_B + V_{BE}, \qquad I_B = I_C/\beta
$$
$$
\Rightarrow\; I_C = \frac{V_{CC} - V_{BE}}{R_C + R_B/\beta}
$$
정밀히는 $V_{BE} = V_T\ln(I_C/I_S)$와 반복 계산.

---

## 4. Common-Emitter (CE)

입력 = base, 출력 = collector. **위상 반전**(이득 부호 −).

$$
A_v \approx -g_m (R_C \parallel r_o) \xrightarrow{r_o\to\infty} -g_m R_C
$$

소스 저항 $R_S$, $r_\pi$ 고려:
$$
A_v = -g_m R_C \frac{r_\pi}{R_S + r_\pi} = -\frac{\beta R_C}{R_S + r_\pi}
$$

- $R_{in} = r_\pi$ (bias 저항 병렬 포함)
- $R_{out} = R_C \parallel r_o$
- 부호 −: $v_{base} \uparrow$ → $I_C \uparrow$ → $R_C$ 강하 ↑ → $V_C \downarrow$.

### Emitter degeneration이 있는 CE
emitter에 $R_E$ 삽입:
$$
A_v \approx -\frac{R_C}{R_E + 1/g_m} \qquad (r_\pi/\beta \approx 1/g_m \text{ 사용})
$$
좀 더 정확히 $A_v \approx -\dfrac{R_C}{R_E + 1/g_m + R_S/\beta}$.

| Degeneration 효과 | |
|---|---|
| 이득 | **감소** ($R_E \gg 1/g_m$이면 $\approx -R_C/R_E$, $g_m$·$\beta$에 둔감) |
| 선형성 | 향상 (local feedback) |
| 입력저항 | 증가 → $r_\pi + (\beta+1)R_E$ |
| 동작점 | 안정화 |
| 대역폭 | 향상 (Miller 감소) |

---

## 5. Common-Base (CB)

입력 = emitter, 출력 = collector. 이득 **양수**.

$$
R_{in} \approx \frac{1}{g_m}, \qquad R_{out} \approx R_C, \qquad A_v \approx g_m R_C
$$

- 낮은 입력저항 → 전류 입력에 적합 (current buffer, $A_i \approx 1$)
- 높은 출력저항, Miller 효과 없음 → **고주파** 특성 우수 (cascode의 상단)

## 6. Common-Collector (CC) = Emitter Follower

입력 = base, 출력 = emitter. 이득 **≈ 1** (약간 작음), 양수.

$$
A_v \approx \frac{R_L}{R_L + 1/g_m} \;\lesssim 1
$$

- $R_{in} \approx r_\pi + (\beta+1)(R_L \parallel r_o)$ → **높음**
- $R_{out} \approx \dfrac{1}{g_m} + \dfrac{R_S}{\beta+1}$ → **낮음**
- 용도: **voltage buffer** (고임피던스 소스 → 저임피던스 부하), level shift ($V_{out} = V_{in} - V_{BE}$)

## 7. 토폴로지 비교

| 구성 | 입력 | 출력 | $A_v$ 부호 | $R_{in}$ | $R_{out}$ | 용도 |
|---|---|---|---:|---:|---:|---|
| **CE** | base | collector | − | 중간 ($r_\pi$) | 높음 ($R_C\parallel r_o$) | 전압 증폭 |
| **CB** | emitter | collector | + | 낮음 ($1/g_m$) | 높음 ($R_C$) | 전류 버퍼, 고주파 |
| **CC** | base | emitter | + (≈1) | 높음 | 낮음 ($1/g_m$) | 전압 버퍼 |

---

## 8. 다단 증폭기

각 단을 따로 계산 후 곱: $A_v = A_{v1}A_{v2}\cdots A_{vn}$.

절차:
1. 1단 이득을 다음 단 고려 없이 계산
2. 1단 출력저항(Thevenin)을 2단의 구동저항으로
3. 2단 이득 계산 (2단 $R_{in}$이 1단의 부하)
4. 전체 = 곱

**loading effect**: 이전 단 $R_{out}$과 다음 단 $R_{in}$이 서로 분압을 만든다.

### 토폴로지를 섞는 이유
한 구성으로 모든 요구 충족 불가:
- 큰 전압 이득 → CE (또는 CS)
- 낮은 $R_{in}$·높은 $R_{out}$ → CB
- 높은 $R_{in}$·낮은 $R_{out}$ → CC
- 예: **CE → CC** = 큰 이득 만든 뒤 부하 구동. **CE → CB (cascode)** = 대역폭·$R_{out}$ 향상.

---

## 핵심 정리

- 증폭기 설계 = 이득 + $R_{in}$ + $R_{out}$ (loading을 항상 고려).
- 소신호 파라미터는 DC 동작점에서 결정 (`gm=IC/VT`, `rπ=β/gm`, `ro=VA/IC`).
- Reduction theorem: emitter $R_E \to$ base에서 $(\beta+1)R_E$; base $R_B \to$ emitter에서 $R_B/(\beta+1)$.
- CE: $-g_m(R_C\parallel r_o)$, 위상 반전. Degeneration: $-R_C/(R_E+1/g_m)$, 이득↓ 선형성·$R_{in}$·대역폭↑.
- CB: $+g_m R_C$, $R_{in}\approx 1/g_m$. CC: $A_v\lesssim1$, $R_{in}$↑ $R_{out}\approx1/g_m$ (버퍼).
- 다단: 단별 이득의 곱, 단 사이 loading 반영.

## 복습 질문

- 전압 증폭기가 $R_{in}$ 크고 $R_{out}$ 작아야 하는 이유를 분압식으로 설명할 수 있나?
- CE 이득 $-g_m(R_C\parallel r_o)$를 소신호 등가회로에서 유도하고, 부호가 −인 이유는?
- emitter degeneration이 이득·입력저항·선형성에 주는 영향과, $R_E \gg 1/g_m$일 때 이득 근사는?
- CE / CB / CC의 입출력 단자, 이득 부호·크기, $R_{in}$·$R_{out}$을 표로 재현할 수 있나?
- 2단 증폭기에서 loading effect를 반영해 전체 이득을 구하는 절차는?
{% endraw %}

---

이전: [03. BJT 기본 동작](03-bjt-operation.md) · 다음: [05. MOSFET 기본 동작](05-mosfet-operation.md)
