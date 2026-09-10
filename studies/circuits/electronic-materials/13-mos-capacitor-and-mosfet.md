---
layout: page
title: "13. MOS 커패시터와 MOSFET"
permalink: /studies/circuits/electronic-materials/13-mos-capacitor-and-mosfet/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes) · 교재: Kasap Ch.6
- 이전: [12. PN 다이오드](12-pn-diode-operation-and-special-diodes.md)

{% raw %}
## 개요

```text
MOS capacitor: gate V로 semiconductor surface charge 조절
ideal MOS 가정 → real MOS: Φ_MS + interface charge → V_FB
accumulation / depletion / inversion
MOSFET: gate field가 channel 형성·조절, inversion에서만 전류
scaling → SCE, HCE, DIBL, gate leakage
```

---

## 1. Ideal MOS Capacitor

- **MIM**: metal–insulator–metal / **MOS**: metal–insulator(SiO₂)–semiconductor
$$
C = \frac{\varepsilon A}{d} = \frac{dQ}{dV}
$$
MOS capacitance = gate voltage 변화에 대한 **semiconductor surface charge 변화**. metal엔 자유전자 많고, semiconductor엔 mobile carrier + fixed ionized dopant.

### Ideal MOS 가정
- metallic gate가 bias를 잘 전달
- oxide가 **완전 절연체**
- SiO₂ 내부·SiO₂/semiconductor interface에 **charge center 없음**
- semiconductor **균일 doping**
- substrate가 두꺼워 내부 field-free region
- back contact가 **Ohmic**

## 2. Real MOS Capacitor

$$\Phi_M \ne \Phi_S$$ → $$V_G = 0$$에서도 band bending. band를 평탄하게 만드는 gate voltage = **flat-band voltage**:
$$
V_{FB} = \Phi_{MS} - \frac{Q_i}{C_{ox}}, \qquad \Phi_{MS} = \Phi_M - \Phi_S
$$
$$Q_i$$ = interface state charge density, $$C_{ox} = \varepsilon_{ox}/t_{ox}$$. interface charge가 있으면 work function 차만으로 예측한 $$V_{FB}$$에서 추가 shift.

## 3. MOS Capacitor Operation

### p-type substrate

| Gate bias | 동작 | 표면 |
|---|---|---|
| $$V_G < 0$$ | **accumulation** | hole 축적 |
| 작은 $$V_G > 0$$ | **depletion** | hole 감소, ionized acceptor 노출 |
| 큰 $$V_G > 0$$ ($$> V_{TH}$$) | **inversion** | electron inversion layer |

### n-type substrate

| Gate bias | 동작 | 표면 |
|---|---|---|
| $$V_G > 0$$ | accumulation | electron 축적 |
| 작은 $$V_G < 0$$ | depletion | electron 감소, ionized donor 노출 |
| 큰 $$V_G < 0$$ | inversion | hole inversion layer |

### Depletion width
- depletion에서 majority carrier 농도 < background doping 농도
- strong inversion 이후 depletion width는 **최대값 $$W_{max}$$**에서 포화, 이후 inversion charge가 주로 증가
$$
W_{max} = \sqrt{\frac{4\varepsilon_s \phi_F}{q N_A}}, \qquad \phi_F = V_T \ln\frac{N_A}{n_i}
$$
- doping ↑ → $$W_{max}$$ ↓

## 4. MOSFET Fundamentals

### 발명 흐름
Audion vacuum tube(정류·증폭 but 크기·전력·발열·신뢰성 문제) → Bell Labs (Bardeen/Brattain/Shockley) Ge point-contact transistor → BJT → JFET → **1959년 Kahng & Atalla MOSFET** → 낮은 전력·높은 집적도·field effect 제어 → 현대 IC 기본 소자.

### 구조
- 기본 terminal: source, drain, gate (+ body/substrate)
- gate는 oxide로 channel과 절연되지만 전기장으로 channel charge 제어
- accumulation·depletion만으로는 source–drain 사이 도전 channel 불충분 → **inversion이 형성되어야 전류**

## 5. NMOSFET vs PMOSFET

| 항목 | NMOSFET | PMOSFET |
|---|---|---|
| substrate | p-type / P-well | n-type / N-well |
| source/drain | n⁺ | p⁺ |
| channel | electron inversion | hole inversion |
| 동작 bias | $$V_{GS} > V_{TH}$$, $$V_{DS} > 0$$ | $$V_{GS} < V_{TH}$$, $$V_{DS} < 0$$ |
| conventional current | drain → source | source → drain |
| 전류 수준 | 큼 | **작음** (hole mobility) |

강의: **NMOS current ≈ PMOS current × 2** (electron mobility > hole mobility).

## 6. MOSFET Operation

### Linear region
$$V_D < V_{D,sat}$$: channel이 resistor처럼, $$V_D$$ ↑ → $$I_D$$ 거의 비례 증가.
$$
V_{D,sat} = V_G - V_{TH}
$$

### Pinch-off와 saturation
- $$V_D = V_{D,sat}$$: drain 끝 inversion charge ≈ 0 → **pinch-off 시작**
- $$V_D > V_{D,sat}$$: pinch-off region 생기지만 source→drain 도달 전자 수 거의 불변 → $$I_D$$ 포화. pinch-off 지점은 electric field·전자 속도 매우 큼.

### Inversion charge
$$
Q_i(x) = -C_{ox}\big[V_{GS} - V_T - V(x)\big]
$$
$$V(x) = V_{GS} - V_T$$인 위치에서 $$Q_i(x) \approx 0$$ → pinch-off. (실제로 완전히 0이 아니라 매우 작아지고 전자가 high-field region을 빠르게 통과.)

## 7. I–V Characteristics & Parameter Extraction

- **transfer characteristics**: $$V_D$$ 고정, $$V_G$$ 스윕
- **output characteristics**: $$V_G$$ 고정, $$V_D$$ 스윕

### Subthreshold swing
$$
S.S = \frac{dV_G}{d(\log_{10} I_D)} \quad [\text{V/dec}]
$$
$$I_D$$를 한 decade 바꾸는 데 필요한 $$V_G$$ 변화. **작을수록** off→on 전환이 급격 → 낮은 전압 동작·낮은 off leakage에 유리. 이론적 하한 **~60 mV/dec** (상온, $$kT/q \cdot \ln 10$$).

### Linear region 추출
$$
I_D = \mu_{lin} C_{ox}\frac{W}{L}\left[(V_G - V_{TH})V_D - \frac{V_D^2}{2}\right]
$$
$$
G_m = \frac{dI_D}{dV_G} = \mu_{lin} C_{ox}\frac{W}{L}V_D, \qquad
\mu_{lin} = \frac{G_{m,max}}{C_{ox} V_D (W/L)}, \qquad
V_{TH,lin} = V_G - \frac{I_D}{G_m} - \frac{V_D}{2}\bigg|_{G_{m,max}}
$$

### Saturation region 추출
$$
I_D = \mu_{sat} C_{ox}\frac{W}{2L}(V_G - V_{TH})^2
$$
$$
\text{Grad} = \frac{d\sqrt{I_D}}{dV_G} = \sqrt{\mu_{sat} C_{ox}\frac{W}{2L}}, \qquad
\mu_{sat} = \frac{1}{C_{ox}}\frac{2L}{W}(\text{Grad}_{max})^2, \qquad
V_{TH,sat} = V_G - \frac{\sqrt{I_D}}{\text{Grad}}\bigg|_{\text{Grad}_{max}}
$$
($$\sqrt{I_D}$$ vs $$V_G$$의 선형성 이용.)

## 8. High Drain Current를 얻는 방법

$$
I_D = \frac{\mu C_{ox}}{2}\frac{W}{L}(V_{GS} - V_{TH})^2
$$

| 방법 | 효과 | 대가 |
|---|---|---|
| $$V_{TH}$$ ↓ | on-current ↑ | **off-current·standby power ↑** |
| $$W$$ ↑ | current ↑ | 면적 ↑ (scaling 불리) |
| $$L$$ ↓ | current ↑ | **short-channel effect·leakage ↑** |

## 9. MOSFET Scaling Issues

### Scaling의 필요성
채널 길이·oxide 두께 ↓ → 집적도 ↑; parasitic capacitance·동작 전압 ↓ → power ↓.

| 문제 | 원인 | 결과 |
|---|---|---|
| **SCE (Short Channel Effect)** | 짧은 channel → 같은 $$V_{DS}$$에 channel field ↑; source/drain depletion이 가까워짐 | **punch-through leakage**, drain이 channel barrier를 강하게 흔듦 |
| **HCE (Hot Carrier Effect)** | high field에서 전자가 큰 에너지 → gate oxide에 주입 | oxide trap·interface state → $$S.S$$, $$V_{th}$$, mobility 열화 |
| **DIBL (Drain-Induced Barrier Lowering)** | short channel에서 높은 $$V_D$$가 source–drain barrier를 낮춤 | gate bias 없어도 $$V_{th}$$ ↓, $$S.S$$ 악화 |
| **Gate leakage** | oxide가 얇아짐 | standby power·reliability ↓ |

### Gate leakage 메커니즘

| 메커니즘 | 조건 |
|---|---|
| Thermionic emission | 온도 의존, 열에너지 큰 전자가 barrier 넘음 |
| Fowler–Nordheim tunneling | 높은 electric field, triangular barrier 터널링 |
| Direct tunneling | ultra-thin oxide 직접 터널링 (→ high-k dielectric으로 대응) |

---

## 복습 질문

- ideal MOS capacitor 가정과, real MOS의 flat-band voltage $$V_{FB} = \Phi_{MS} - Q_i/C_{ox}$$의 의미는?
- p-type / n-type substrate에서 accumulation / depletion / inversion의 gate bias 조건은?
- MOSFET이 inversion channel에서만 전류를 잘 흘리는 이유, pinch-off와 saturation의 물리적 의미는?
- subthreshold swing의 정의와 이론적 하한(~60 mV/dec), 작을수록 좋은 이유는?
- linear·saturation region에서 $$\mu$$와 $$V_{TH}$$를 추출하는 식은?
- SCE / HCE / DIBL / gate leakage의 원인과 결과를 각각 설명할 수 있나?
{% endraw %}

---

이전: [12. PN 다이오드](12-pn-diode-operation-and-special-diodes.md)
