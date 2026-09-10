---
layout: page
title: "02. 결정구조와 결함 (Crystal Structure & Defects)"
permalink: /studies/circuits/electronic-materials/02-crystal-structure-and-defects/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes) · 교재: Kasap Ch.1
- 이전: [01. 원자모형과 결합](01-atomic-model-and-bonding.md) · 다음: [03. 결정질·비정질·공정](03-crystalline-amorphous-and-fabrication.md)

{% raw %}
## 개요

```text
crystal structure = lattice + basis → unit cell
→ BCC/FCC/HCP/diamond/zinc blende/NaCl/CsCl → 충진율·배위수
→ Miller index [uvw]·(hkl) → 점 결함 + 선 결함 → 확산·저항·강도·성장
```

---

## 1. 열적으로 활성화된 과정

한 안정 상태 → 다른 안정 상태로 가려면 에너지 장벽 $$E_A$$를 넘어야 한다. 원자 확산, 불순물 이동, vacancy 이동이 대표적:
$$
\text{rate} \propto \nu_0\, e^{-E_A/kT}
$$
온도 ↑ → $$E_A$$ 넘는 입자 비율 급증 → 확산·반응 속도 ↑.

## 2. Lattice · Basis · Unit Cell

| 용어 | 의미 |
|---|---|
| **lattice** | 공간에 주기적으로 반복되는 기하학적 점 배열 |
| **basis** | 각 lattice point에 붙는 동일한 원자/분자 그룹 |
| **crystal structure** | lattice + basis |
| **unit cell** | 반복으로 전체 결정을 만드는 최소 셀 |

격자 상수: 변 길이 $$a, b, c$$ + 각도 $$\alpha, \beta, \gamma$$ → 7개 결정계.

## 3. 대표 결정 구조

| 구조 | 특징 | 충진율(APF) / 배위수 | 예 |
|---|---|---|---|
| **BCC** | 모서리 8 + 중심 1 | 68% / 8 | Fe, W |
| **FCC** | 모서리 + 면 중심 | 74% (close-packed) / 12 | Cu, Al, Au |
| **HCP** | 육방 조밀 | 74% / 12 | Mg, Zn |
| **Diamond cubic** | 사면체 공유결합 네트워크, 단위격자 8원자 | 34% / 4 | Si, Ge, C(diamond) |
| **Zinc blende** | diamond cubic + 두 종류 원자 교대 | — / 4 | GaAs, ZnS, InP (III–V, II–VI) |
| **NaCl (rock salt)** | 상호 침투 두 FCC 이온 격자 | — / 6 | NaCl, MgO |
| **CsCl** | 한 이온 중심, 반대 이온 모서리 | — / 8 | CsCl |

**배위수** = 최근접 이웃 수. 이온 결정에서는 **반지름 비 $$r_+/r_-$$**가 가능한 배위수·구조를 제한 (radius ratio rule).

## 4. Miller Index

### 방향 [uvw]
1. 축 x, y, z에 대한 방향 벡터 성분
2. 분수 → 최소 정수비로 배수화
3. 음수는 bar 표기 ($$\bar{1}$$)
4. 대괄호 `[uvw]`; 등가 방향 집합 `<uvw>` (예: cubic의 `<111>`)

### 면 (hkl)
1. x, y, z축과의 절편
2. 각 절편의 **역수**
3. 최소 정수비
4. 소괄호 `(hkl)`; 등가 면 집합 `{hkl}`

### 면 원자 농도
$$
\text{planar concentration} = \frac{\text{면 위에 중심이 놓인 원자 수}}{\text{면적}}
$$
같은 결정도 (100), (110), (111) 면의 밀도가 달라 → **표면 반응성·식각 속도·성장 특성**이 다르다. (Si (100) vs (111): MOS 계면은 (100) 선호.)

## 5. 동소체 / 다형성

**Allotropy / polymorphism** — 같은 물질이 둘 이상의 결정 구조. 탄소: diamond(sp³, 절연·경질), graphite(sp², 도전·연질), fullerene, CNT, graphene. 구조가 바뀌면 밀도·탄성률·전기전도·광학 특성이 크게 달라진다.

## 6. 결정 결함

### 점 결함

| 결함 | 설명 |
|---|---|
| **Vacancy** | 빈 격자점 (표면 원자가 이동). 농도 $$\propto e^{-E_v/kT}$$ — 온도 민감 |
| **Substitutional impurity** | 불순물이 host 자리 대체 |
| **Interstitial impurity** | 불순물이 host 원자 사이 빈 공간 |
| **Schottky defect** | 이온 결정에서 이온이 표면으로 이동 → vacancy (전하 중성 유지 위해 양·음이온 쌍) |
| **Frenkel defect** | host ion이 interstitial로 이동 → 원위치 vacancy |

원자 크기 차 클수록 격자 왜곡·산란 ↑.

### 선 결함

| 결함 | 설명 |
|---|---|
| **Edge dislocation** | 원자 반평면이 중간에 끝남 → 위 압축 / 아래 인장 strain field |
| **Screw dislocation** | 결정 일부가 원자 간격만큼 전단 → 나선 계단 → **결정 성장의 핵심 site** (새 원자가 2~3개 결합 동시 형성) |

결함 주변 strain field → 전자·phonon 산란 증가 → 저항 ↑, 열전도 ↓.

---

## 복습 질문

- lattice / basis / unit cell의 차이, "crystal = lattice + basis"의 의미는?
- BCC(68%) / FCC·HCP(74%) / diamond(34%)의 충진율·배위수 차이, diamond cubic·zinc blende가 반도체에서 중요한 이유는?
- Miller 방향 [uvw]와 면 (hkl)을 구하는 절차, (100)/(110)/(111)면의 원자 밀도가 왜 중요한가?
- Schottky defect와 Frenkel defect의 차이는?
- screw dislocation이 결정 성장을 촉진하는 이유는?
{% endraw %}

---

이전: [01. 원자모형과 결합](01-atomic-model-and-bonding.md) · 다음: [03. 결정질·비정질·공정](03-crystalline-amorphous-and-fabrication.md)
