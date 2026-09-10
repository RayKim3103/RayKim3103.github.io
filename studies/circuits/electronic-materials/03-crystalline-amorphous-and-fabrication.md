---
layout: page
title: "03. 결정질·비정질·공정 (Crystalline / Amorphous / Fabrication)"
permalink: /studies/circuits/electronic-materials/03-crystalline-amorphous-and-fabrication/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes) · 교재: Kasap Ch.1
- 이전: [02. 결정구조와 결함](02-crystal-structure-and-defects.md) · 다음: [04. 빛과 양자 이중성](04-light-and-quantum-duality.md)

{% raw %}
## 개요

```text
결정질(장거리 질서) / 비정질(단거리 질서) / 다결정(grain + grain boundary)
a-Si vs a-Si:H (dangling bond passivation)
Czochralski 단결정 성장, quenching·melt spinning·PVD·CVD
solid solution·zone refining, interconnect·electromigration
```

---

## 1. 고체의 세 가지 상태

| 상태 | 구조 | 특징 |
|---|---|---|
| **Crystalline** | 원자 주기 배열 | 장거리 질서, 뚜렷한 melting point $$T_m$$ |
| **Amorphous** | 무작위에 가까움 | 단거리 질서만, glass transition $$T_g$$ (연속적 변화) |
| **Polycrystalline** | 작은 grain들이 서로 다른 방향 | grain boundary 존재, 국소적으로만 결정성 |

비정질에는 **void, strained bond, dangling bond**가 많아 → 전자 상태가 band gap 내부(gap state)로 들어옴.

## 2. 결정질 Si / a-Si / a-Si:H

| | 결정질 Si | a-Si | a-Si:H |
|---|---|---|---|
| 결합 | sp³ 사면체, 네 이웃 | 장거리 질서 없음, dangling bond·void | H가 dangling bond 포화 |
| band | 잘 정의된 VB/CB | band edge에 **localized state** (band tail), 이동도 낮음 | 결함 상태 밀도 감소 |
| dangling bond | 거의 없음 | recombination center / trap | passivation됨 |
| 응용 | IC, wafer | — | 박막 태양전지, 디스플레이 TFT |

## 3. Grain과 Grain Boundary

다결정 = 핵생성 후 성장한 grain들이 만나 형성. grain마다 방향이 달라 경계에서 자연스러운 결합 불가:
- broken bond, vacancy, void, strained bond, interstitial-type atom
- grain boundary 원자는 내부보다 **높은 에너지** → 전하 trap·산란 중심
→ 다결정 박막 저항률이 단결정보다 높음 ([06. 전도](06-electrical-and-thermal-conduction.md)).

## 4. 표면과 화학양론

- **표면 원자**: 결합을 다 못 채워 dangling bond → surface **reconstruction**으로 완화. physisorption / chemisorption이 박막 성장·계면 특성 결정.
- **Stoichiometric** compound: 양·음이온이 정확한 비율. **Non-stoichiometric**: 한 성분 과잉/결함 (예: ZnO에서 과잉 Zn이 interstitial Zn²⁺ → 자유전자, n-type).

## 5. Czochralski 단결정 성장

1. 고순도 polysilicon 용융 (~1414°C)
2. dopant 첨가로 전기적 특성 조절
3. **seed crystal**을 용융 Si에 접촉
4. seed를 회전시키며 서서히 끌어올려 단결정 **ingot** 성장
5. ingot 절단·연마 → wafer

- ingot 내부 원자는 diamond cubic lattice에 주기 배열
- wafer 두께 수백 µm, 결정 방향은 **flat 또는 notch**로 표시
- (Float-Zone 방식은 도가니 오염 없이 더 고순도, 대구경엔 CZ)

## 6. 비정질 제조

### 냉각 속도와 상 변화
- 느린 냉각: 원자 재배열 → 결정질
- **빠른 냉각(quenching)**: 결정화 시간 부족 → supercooled liquid → amorphous solid
- 결정질은 뚜렷한 $$T_m$$, 비정질은 $$T_g$$ 중심 연속 변화

### 방법

| 방법 | 원리 |
|---|---|
| **Melt spinning** | 용융 합금을 빠르게 냉각 → metallic glass |
| **PVD — e-beam evaporation** | 전자빔으로 Si 국부 가열·증발 → 기판 응축 |
| **PVD — sputtering** | plasma ion이 target을 때려 원자 방출 → substrate 증착 |
| **CVD (PECVD)** | RF plasma에서 silane(SiH₄) 분해 → Si + H 응축 → a-Si:H (H가 dangling bond passivation) |

## 7. 고용체와 상평형

- **Solid solution** = 한 상 안에서 조성·구조·성질이 균일한 합금. substitutional / interstitial.
- **Cu–Ni isomorphous alloy**: 냉각 조건에 따라 균일 조성 또는 grain 내부 segregation. 빠른 냉각 → 먼저·나중 고화 영역의 조성 차 → grain boundary 근처 편차 (coring).

### Zone refining
국부 용융 영역을 이동 → 불순물이 **액상에 더 많이 잔류** (segregation coefficient $$k < 1$$) → 재고화된 고체의 불순물 농도 낮음. 반복 → 불순물이 한쪽 끝으로 몰려 순도 ↑.

## 8. Microelectronic Interconnect

- IC는 M1, M2, M3 … 여러 금속 배선층 + via로 소자 연결
- 배선 사이 **수직 capacitance $$C_V$$ + 수평 capacitance $$C_H$$**
- 미세화 → RC delay, crosstalk, **electromigration** ↑

### Electromigration
높은 전류밀도에서 전자가 금속 이온에 운동량 전달 → 이온이 서서히 이동 (electron wind force). grain boundary·interface를 따라:
- **void** 성장 → 배선 단선
- **hillock** 형성 → 단락·신뢰성 저하

Black's equation: MTTF $$\propto J^{-n} e^{E_a/kT}$$. → Cu(높은 EM 저항) + barrier layer.

---

## 복습 질문

- 결정질 / 비정질 / 다결정의 구조 차이, 비정질에서 gap state가 생기는 이유는?
- a-Si와 a-Si:H에서 dangling bond와 localized state의 의미, H의 역할은?
- Czochralski 공정 순서와 seed crystal의 역할은?
- quenching / melt spinning / PVD / CVD가 각각 무엇을 만드나?
- zone refining이 순도를 높이는 원리($$k < 1$$)는? electromigration이 배선을 망가뜨리는 과정은?
{% endraw %}

---

이전: [02. 결정구조와 결함](02-crystal-structure-and-defects.md) · 다음: [04. 빛과 양자 이중성](04-light-and-quantum-duality.md)
