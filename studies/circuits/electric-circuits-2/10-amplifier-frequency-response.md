---
layout: page
title: "10. 증폭기 주파수 응답 — CS · Degeneration · CG · Cascode · SF · Differential"
permalink: /studies/circuits/electric-circuits-2/10-amplifier-frequency-response/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `12 Frequency Response of CS` + `13 CS Degeneration and CG` + `14 Cascode, SF, Differential` **통합**

{% raw %}
## 개요

MOS 증폭기의 주파수 응답을 topology별로 비교한다. 공통 도구는 **Miller theorem**으로 input↔output을 잇는 $C_{gd}$를 각 node의 ground capacitance로 나누는 것. CS는 큰 negative gain 때문에 input Miller capacitance가 커져 **dominant input pole**이 생기고, CG·cascode·SF는 Miller multiplication이 작아 더 빠르다.

```text
CS: Cgd Miller multiplication → GBW trade-off
coupling/bypass capacitor → low-frequency pole/zero
CG, cascode, SF: Miller 작음 → wide bandwidth
differential: DM은 CS와 유사, CM은 고주파에서 악화
```

---

# Part 1. Common-Source 주파수 응답

## 1. 고려할 capacitance

$C_{gs}$, $C_{gd}$, $C_{db}$. **$C_{gd}$가 input–output을 직접 연결** → node coupling → 해석 복잡.

## 2. Miller's Theorem

두 node 사이 impedance가 있고 voltage gain $A_v = V_{out}/V_{in}$이면, floating capacitor $C_F$를:
$$
C_{in,Miller} = C_F(1 - A_v), \qquad C_{out,Miller} \approx C_F\left(1 - \frac{1}{A_v}\right)
$$
CS는 $A_v < 0$ 이므로:
$$
C_{in,Miller} \approx C_{gd}(1 + |A_v|)
$$
→ **Miller effect**: input에서 $C_{gd}$가 $(1+|A_v|)$배로 보인다.

## 3. 등가 Capacitance

$A_v \approx -g_m R_L$ 라 하면:
$$
C_X \approx C_{gs} + C_{gd}(1 + g_m R_L) \quad(\text{input node})
$$
$$
C_Y \approx C_{db} + C_{gd}\left(1 + \frac{1}{g_m R_L}\right) \quad(\text{output node})
$$
gain이 크면 input Miller capacitance가 매우 커진다.

## 4. 근사 전달함수와 pole

$$
\frac{V_{out}}{V_{in}} \approx \frac{-g_m R_L}{(1 + s R_S C_X)(1 + s R_L C_Y)}
$$
$$
\omega_{p,in} \approx \frac{1}{R_S\big[C_{gs} + C_{gd}(1 + g_m R_L)\big]}, \qquad
\omega_{p,out} \approx \frac{1}{R_L\big[C_{db} + C_{gd}(1 + 1/(g_m R_L))\big]}
$$

## 5. Dominant Pole & GBW

CS는 보통 **input Miller pole이 dominant**. 큰 gain 조건:
$$
\omega_{p,in} \approx \frac{1}{R_S\, C_{gd}\, g_m R_L}
$$
→ **gain이 커질수록 bandwidth 감소**. Gain–Bandwidth Product:
$$
\text{GBW} \approx \frac{g_m}{C_{gd}}\cdot(\text{$R_S$ factor})
$$
핵심 메시지: gain을 키우면 Miller effect로 bandwidth가 줄어드는 **gain–bandwidth trade-off**.

---

# Part 2. CS with Degeneration & Common-Gate

## 6. Coupling Capacitor (저주파)

입력 coupling capacitor $C_i$는 DC 차단, AC 통과 → **high-pass**:
$$
\frac{V_x}{V_{in}} = \frac{s(R_{sig}+R_{in})C_i}{1 + s(R_{sig}+R_{in})C_i}, \qquad
\omega_{p,in} = \frac{1}{(R_{sig}+R_{in})C_i}
$$
관심 최소 주파수 $\omega_{min}$에서 충분히 short처럼 보이려면 $C_i > 1/[(R_{sig}+R_{in})\omega_{min}]$.

## 7. Bypassed Degeneration

source resistor $R_S$에 bypass capacitor $C_b$ 병렬:

| 주파수 | $C_b$ | gain |
|---|---|---|
| 저주파 | open | $-g_m R_D/(1+g_m R_S)$ (degeneration) |
| 고주파 | short | $-g_m R_D$ (회복) |

전달함수:
$$
\frac{V_{out}}{V_x} = \frac{-g_m R_D(1 + s R_S C_b)}{1 + g_m R_S + s R_S C_b}
$$
$$
\omega_{z,out} = \frac{1}{R_S C_b}, \qquad \omega_{p,out} = \frac{1 + g_m R_S}{R_S C_b}
$$
→ 주파수가 올라가면 gain이 $-g_m R_D/(1+g_m R_S)$에서 $-g_m R_D$로 증가.

## 8. 고주파에서의 CS with Degeneration

$C_{gd}$ Miller pole이 다시 지배:
$$
\omega_p \approx \frac{1}{(R_{sig}\parallel R_{in})\,C_{gd}(1 + g_m R_D)}
$$
→ Part 1과 같은 Miller effect.

## 9. Common-Gate 주파수 응답

CG는 input–output을 직접 잇는 capacitor가 없다고 단순화 → **Miller effect 작음**:
$$
\omega_{p,in} \approx \frac{g_m R_S + 1}{R_S(C_{gs}+C_{sb})}
\;\;=\;\; \frac{1}{(R_S \parallel 1/g_m)(C_{gs}+C_{sb})}
$$
$$
\omega_{p,out} \approx \frac{1}{R_D(C_{gd}+C_{db})}
$$

### CG가 CS보다 빠른 이유
| | CS | CG |
|---|---|---|
| $C_{gd}$ | input↔output 연결 → Miller | gate AC ground → output-to-ground capacitance |
| input capacitance | $C_{gd}(1+\|A_v\|)$로 증배 | Miller multiplication 거의 없음 |
→ 일반적으로 **CG bandwidth > CS bandwidth**.

---

# Part 3. Cascode · Source Follower · Differential

## 10. Cascode 주파수 응답

CS(M1) + CG(M2). M1의 drain(내부 node Y) 전압 변화가 작아서 $C_{gd1}$은 큰 Miller 증배 대신 대략
$$
\text{effective } C_{gd1} \approx C_{gd1}\left(1 + \frac{g_{m1}}{g_{m2}}\right)
$$
정도의 **작은 factor**로만 보인다.

주요 node: input / internal Y / output.
$$
\omega_{p,out} \approx \frac{1}{R_L(C_{db} + C_{gd})}
$$
내부 node Y는 저항이 낮아($\sim 1/g_{m2}$) pole이 고주파로 밀림.

### Razavi 예시 비교
| | 3-dB bandwidth |
|---|---|
| CS | 약 250 MHz |
| Cascode | 약 440 MHz |
→ cascode는 gain도 크고 bandwidth도 개선. 단점은 headroom·output swing.

## 11. Source Follower 주파수 응답

$A_v \approx 1$ → input Miller effect 거의 없음:
$$
C_{in} \approx C_{gd} + C_{gs}(1 - A_v) \approx C_{gd} \;\;(\text{작음})
$$
$$
C_{out} \approx C_{sb} + C_L, \qquad \omega_{p,out} \approx \frac{g_m}{C_{sb} + C_L}
$$
→ SF는 보통 CS보다 bandwidth가 크고, **load capacitance $C_L$에 의해 제한**되기 쉽다. (SF는 특정 조건에서 입력 임피던스가 음의 실수부를 가져 ring할 수 있으니 주의.)

## 12. Differential Amplifier

### DM 응답
half-circuit = CS amplifier:
$$
A_{dm} \approx -g_m R_D, \qquad
\omega_p \approx \frac{1}{R_S\big[C_{gs} + C_{gd}(1 + g_m R_D)\big]}
$$
→ CS와 동일한 $C_{gd}$ Miller effect.

### CM 응답
tail current source가 MOSFET → **finite resistance + capacitance**.
- **저주파**: tail source의 큰 resistance → common-mode gain 작음 (CMRR 좋음)
- **고주파**: tail node capacitance 때문에 tail이 충분히 고정 안 됨 → **common-mode gain 증가 → 고주파 CMRR 저하**

---

## 핵심 정리

- **Miller theorem**: $C_{in,Miller} = C_F(1-A_v)$. CS에서 $C_{gd}(1+|A_v|)$ → dominant input pole → **GBW trade-off**.
- **Coupling capacitor** → high-pass pole $1/[(R_{sig}+R_{in})C_i]$. **Bypass capacitor** → zero $1/(R_S C_b)$, pole $(1+g_m R_S)/(R_S C_b)$, 저주파 gain↓ 고주파 gain↑.
- **CG**: Miller 작음 → CS보다 빠름. **Cascode**: 내부 node 저항 낮아 Miller 억제 (예: 250→440 MHz). **SF**: $A_v\approx1$ → Miller 없음, $C_L$이 BW 제한.
- **Differential**: DM은 CS와 동일, CM은 tail capacitance로 고주파에서 악화.

## 복습 질문

- Miller theorem으로 CS의 $C_X$, $C_Y$를 유도하고, input pole이 dominant인 이유는?
- coupling capacitor와 bypass capacitor가 만드는 pole/zero의 종류와 위치는?
- CG·cascode·SF가 각각 왜 CS보다 빠른가? (Miller 관점)
- differential amplifier의 CM 응답이 고주파에서 악화되어 CMRR이 떨어지는 이유는?
{% endraw %}

---

이전: [09. MOSFET 고주파 모델](09-mosfet-high-frequency-model.md) · 다음: [11. OTA & Op-Amp](11-ota-and-op-amp.md)
