---
layout: page
title: "07. Si 박막 결정화 (Silicon Thin-Film Crystallization)"
permalink: /studies/circuits/electronic-materials/07-silicon-thin-film-crystallization-si/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes) · 주제: LTPS / ELA / SLS / TFT
- 이전: [06. 전기·열 전도](06-electrical-and-thermal-conduction.md) · 다음: [08. 분자궤도와 에너지밴드](08-molecular-orbital-and-energy-bands.md)

{% raw %}
## 개요

비정질 Si → poly-Si로 결정화 → TFT 이동도·구동 전류 ↑. 대표 방법: furnace anneal, SPC, RTA, CGS, MILC, **ELA**, **SLS**. 생산 공정에서는 ELA와 SLS가 핵심.

```text
a-Si → (ELA: excimer laser로 표면만 순간 용융·재결정) → poly-Si
grain boundary → TFT V_th ↑, I_off ↑, mobility ↓
laser energy density → partial / complete / near-complete melting (SLG)
```

---

## 1. 결정화 방법

| 방법 | 의미 | 특징 |
|---|---|---|
| **Furnace anneal** | 노 전체 가열 | 긴 시간, 높은 thermal budget |
| **SPC** | solid phase crystallization | 고상에서 결정화 (~600°C, 수 시간) |
| **RTA** | rapid thermal annealing | 짧은 시간 고온 |
| **CGS** | continuous grain Si | 연속 grain |
| **MILC** | metal-induced lateral crystallization | 금속 촉매(Ni 등)로 lateral 결정화 유도, 저온 |
| **ELA** | excimer laser annealing | 표면만 순간 용융 후 재결정 |
| **SLS** | sequential lateral solidification | lateral growth를 순차 제어 |

## 2. Rapid Thermal Annealing (RTA)

- dopant activation, metal contact의 interfacial reaction에 사용
- wafer를 상온 → **1000–1500 K**까지 빠르게 가열, 목표 온도에서 수 초 유지 후 급랭
- 긴 furnace anneal 대비 **diffusion broadening 최소화** (짧은 thermal budget)

## 3. Excimer Laser Annealing (ELA)

### 공정
1. glass substrate 위에 a-Si layer 증착
2. **pulsed rectangular UV laser** (XeCl 308 nm 등) 스캔
3. a-Si 표면층이 순간 용융
4. 냉각 중 재결정화 → poly-Si
5. laser 에너지는 주로 표면 a-Si가 흡수 → **glass substrate는 영향 적음** (저온 공정 → LTPS)

### 장점 / 문제
- 장점: 결정화 시간 매우 짧음, 저온 기판 가능, 다중 shot·overlap으로 grain size 확대 (예: **95% overlap, 20 shots**)
- 문제: OLED / large-area display에서 shot 간 균일도 확보 어려움 → mura

## 4. Poly-Si TFT

- poly-Si = 여러 결정 grain + grain boundary. grain 내부는 결정질에 가깝고, 경계에는 결합 불완전·defect state 다수.
- a-Si보다 carrier conduction 좋아 TFT 성능 ↑, 그러나 **grain boundary 품질이 성능을 제한**.

### Grain boundary 효과
- threshold voltage $$V_{th}$$ ↑
- off current $$I_{off}$$ ↑
- carrier mobility ↓
- trap state 많아 subthreshold 특성 악화

**Transfer curve**: $$V_D$$ 고정, $$V_G$$ 스윕하며 $$I_D$$ 측정. n-type / p-type TFT는 carrier·mobility 차이로 전류 수준·기울기가 다름.

## 5. Energy Density의 역할

### 분석
- **TEM**: ex-situ로 grain 구조
- **TR (transient reflectance)**: in-situ로 melting/solidification dynamics
- energy density별 average grain radius + melt duration → 결정화 regime 판단

### 세 가지 regime

| Energy density | 상태 | 결과 grain |
|---|---|---|
| 낮음 | **partial melting** | vertical regrowth, small grains |
| 너무 높음 | **complete melting** | copious nucleation, fine grains |
| 적절함 | **near-complete melting** | **super lateral growth (SLG)**, large grains |

## 6. Super Lateral Growth (SLG)

거의 완전히 녹은 영역에서 **남은 seed**를 중심으로 lateral growth가 길게 진행.
- seed가 가까움 → lateral growth가 서로 만나 continuous large-grained poly-Si
- seed가 너무 멂 → 완전 용융 영역에서 copious nucleation 먼저 → isolated disk / fine grain

→ **energy density, pulse overlap, seed spacing** 제어가 grain size·균일도에 직결.

## 7. Multiple Pulse & Phase Transformation

- 여러 pulse → melt-mediated grain growth 반복 → grain enlargement 가능. 단 국부 용융·재고화 반복 → **공정 윈도우 좁음**.
- **Heating**: laser pulse → a-Si 온도 급상승, 용융 깊이는 fluence·흡수율.
- **Cooling**: pulse 후 열이 기판·주변으로 → 재고화. 냉각 속도·seed 유무가 nucleation vs growth 경쟁 결정. **Recalescence**: 결정화 잠열 방출로 온도 변화가 일시적으로 완만.

## 8. Grain Boundary 위치 제어

TFT channel 내부에 grain boundary가 놓이면 carrier 경로가 trap·barrier를 만나 성능 편차 ↑. **SLS / artificially controlled SLG**는 grain boundary를 channel 밖으로 유도해 device uniformity 향상. source/drain/gate 배치와 grain boundary 위치의 관계가 최적화 핵심.

---

## 복습 질문

- RTA와 ELA의 공정 차이, ELA가 glass substrate를 크게 손상시키지 않는 이유는?
- grain boundary가 TFT의 $$V_{th}$$, $$I_{off}$$, mobility에 주는 영향은?
- partial melting / complete melting / near-complete melting(SLG) regime의 차이와, energy density와의 관계는?
- SLG에서 seed spacing이 grain size에 미치는 영향은?
- grain boundary 위치 제어(SLS)가 TFT 균일도에 중요한 이유는?
{% endraw %}

---

이전: [06. 전기·열 전도](06-electrical-and-thermal-conduction.md) · 다음: [08. 분자궤도와 에너지밴드](08-molecular-orbital-and-energy-bands.md)
