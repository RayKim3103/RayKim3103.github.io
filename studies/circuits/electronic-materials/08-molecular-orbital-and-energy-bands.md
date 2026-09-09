---
layout: page
title: "08. 분자궤도와 에너지밴드 (Molecular Orbital & Energy Bands)"
permalink: /studies/circuits/electronic-materials/08-molecular-orbital-and-energy-bands/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes) · 교재: Kasap Ch.4
- 이전: [07. Si 박막 결정화](07-silicon-thin-film-crystallization-si.md) · 다음: [09. 페르미 통계·방출·포논](09-fermi-statistics-emission-phonons.md)

{% raw %}
## 개요

```text
원자 궤도 겹침 → bonding / antibonding orbital 분리
N개 원자 → 촘촘한 준위 → energy band
금속: 부분적으로 찬 밴드 / Si: sp³ hybrid → bonding=VB, antibonding=CB
결정 속 전자 응답 → effective mass m*
```

---

## 1. H₂ 분자의 Molecular Orbital

두 H의 1s orbital이 겹침 → 겹침 방식에 따라:
- **bonding orbital $\psi_\sigma$** (낮은 에너지, 원자 사이 전자밀도 ↑ → 핵–전자 인력 ↑ → 안정)
- **antibonding orbital $\psi_\sigma^*$** (높은 에너지, 마디면)

두 전자가 스핀 짝지어 bonding orbital을 채움 → 안정한 H₂.

## 2. H₂ vs He₂

| | 전자 배치 | 결과 |
|---|---|---|
| **H₂** | bonding 2개 (bonding만 채움) | 안정 (bond order 1) |
| **He₂** | bonding 2 + antibonding 2 | 안정화 = 불안정화 상쇄 → **결합 안 됨** (bond order 0) |

핵심 직관: 결합 형성에는 **half-occupied orbital의 겹침**(H₂)이 유리.

## 3. Energy Band 형성

| 원자 수 | 준위 |
|---|---|
| 2 | 1 준위 → 2 준위로 분리 |
| 3 | 3개 molecular orbital |
| $N$ 매우 큼 | 분리 간격이 극히 작아 → 거의 **연속적인 band** |

### Li solid 예시
$N$개 Li → $N$개 2s 전자. 2s band에는 스핀 포함 $2N$개 state. $N$개 전자가 $2N$개 state를 채움 → **2s band는 half-filled** → 전기장에 의해 전자가 빈 상태로 쉽게 이동 → **금속 전도성**.

### 궤도 겹침과 band
원자가 무한히 멀면 discrete atomic level. 가까워질수록 바깥 orbital부터 겹쳐 band 형성 (3s → 3s band, 2p → 2p band). 여러 band가 겹치면 거의 연속.

## 4. 금속의 Energy Band

- valence electron band가 **부분적으로만 참** → Fermi level 근처에 빈 state → 전기장에 전자가 쉽게 에너지 바꾸고 drift.
- 전압 인가 → 전자의 electrostatic potential energy가 위치에 따라 변화 → **energy band가 공간적으로 기울어짐 (band tilting)**. 전류 = Fermi level 근처 전자의 비대칭 점유가 만드는 net motion.

## 5. Si의 전자 구조

### Isolated Si atom
Group IV, 원자가전자 = 3s² 3p². 결합 전엔 3s와 3p가 다른 에너지.

### sp³ hybridization
결합 직전 3s 1개 + 3p 3개 → **네 개의 동일한 sp³ hybrid orbital**, 사면체 방향 (각도 **109.5°**), 각각에 원자가전자 1개 → 이웃 Si와 공유결합.

### Si crystal의 band 형성
이웃 Si의 sp³ hybrid가 겹침 → bonding $\psi_B$ + antibonding $\psi_A$:
- bonding orbital들이 겹쳐 **valence band**
- antibonding orbital들이 겹쳐 **conduction band**
- 0 K: VB 가득, CB 비어 있음
- 두 band 사이 **gap $E_g$** (Si 1.12 eV) → semiconductor 성질

## 6. Semiconductor Energy Band

| | 의미 |
|---|---|
| **CB** | 전자가 이동·전류 기여 |
| **VB** | 결합 전자가 채움 |
| **$E_g$** | VB top ↔ CB bottom 금지 영역 |

0 K: VB 가득 → 빈 state 없어 전류 흐르기 어려움. 유한 온도/빛 → 일부 전자가 CB로 → **electron–hole pair** → 전도성.

## 7. Effective Mass

자유공간: $a = F/m_e$.
결정 속: 주기적 퍼텐셜·band curvature·산란 → 자유전자 질량 대신 **effective mass $m^*$**:
$$
a_{crystal} = \frac{F_{ext}}{m^*}, \qquad m^* = \hbar^2 \left(\frac{d^2 E}{dk^2}\right)^{-1}
$$
- $m^*$는 실제 질량이 바뀐다는 게 아니라 band structure가 가속 응답을 바꾼다는 **모델 파라미터**.
- **band curvature 클수록 $m^*$ 작고 mobility 크다** ($\mu = e\tau/m^*$).
- Si: $m_n^* \approx 0.26\,m_e$, $m_p^* \approx 0.39\,m_e$ (conductivity effective mass).

---

## 복습 질문

- bonding과 antibonding orbital의 에너지·전자밀도 차이, H₂는 결합하는데 He₂는 안 되는 이유는?
- $N$개 원자가 모일 때 discrete level이 band가 되는 과정, Li의 half-filled 2s band가 금속성을 설명하는 방식은?
- Si의 sp³ hybridization(109.5°)과 valence band·conduction band 형성 과정은?
- effective mass가 필요한 이유와 $m^* = \hbar^2/(d^2E/dk^2)$의 의미, band curvature와 mobility의 관계는?
{% endraw %}

---

이전: [07. Si 박막 결정화](07-silicon-thin-film-crystallization-si.md) · 다음: [09. 페르미 통계·방출·포논](09-fermi-statistics-emission-phonons.md)
