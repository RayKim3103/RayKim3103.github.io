---
layout: page
title: "05. 터널링·흑체·레이저 (Tunneling / Blackbody / Laser)"
permalink: /studies/circuits/electronic-materials/05-tunneling-blackbody-laser/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes) · 교재: Kasap Ch.3
- 이전: [04. 빛과 양자 이중성](04-light-and-quantum-duality.md) · 다음: [06. 전기·열 전도](06-electrical-and-thermal-conduction.md)

{% raw %}
## 개요

```text
양자 터널링 (E < V0에서도 통과) → STM (I ∝ exp(-2αa))
흑체복사 → Planck 에너지 양자화
수소 원자 파동함수·E_n → 궤도 각운동량 → 광자 방출·선택규칙
Laser: population inversion + stimulated emission
```

---

## 1. 양자 터널링

고전: $E < V_0$ 이면 장벽 통과 불가.
양자: 전자를 파동함수로 → 장벽 내부에서 $\psi$가 **지수 감쇠**하지만 0은 아님 → 얇은 장벽이면 반대편에 진폭 남아 통과 가능.

장벽 내부 ($E < V_0$): $\;\psi \propto e^{-\alpha x}$, $\;\alpha = \dfrac{\sqrt{2m(V_0 - E)}}{\hbar}$

투과 확률:
$$
T \approx e^{-2\alpha a}
$$

**터널링이 쉬워지는 조건**: 장벽 두께 $a$ 작을수록 · 전자 에너지가 $V_0$에 가까울수록 · **유효질량 작을수록**.

## 2. Scanning Tunneling Microscope (STM)

날카로운 금속 tip ↔ 도전성 시료 사이 bias + 터널링 전류 측정:
$$
I_{tunnel} \propto e^{-2\alpha a}
$$
거리 $a$가 1 Å만 변해도 전류가 ~1 자릿수 변함 → 원자 높이 차를 매우 민감하게 감지. graphite carbon ring, Ni(100) 표면 이미징 가능. STM 신호는 지형 + **전자 상태 밀도(LDOS)**를 함께 반영하므로 단순 지형 측정 이상으로 해석.

## 3. 흑체복사

흑체 = 들어오는 빛을 모두 흡수하고 온도에 따른 스펙트럼으로 복사.
- 온도 ↑ → 총 방출 에너지 ↑ (Stefan–Boltzmann $\propto T^4$)
- 스펙트럼 피크 → 짧은 파장 (Wien: $\lambda_{max} T = $ 상수)

고전(Rayleigh–Jeans)은 자외선 파탄. **Planck**: 에너지 교환이 연속이 아니라 $E = h\nu$ 단위로 양자화 → 해결.

## 4. 수소 원자 파동함수

- 전자는 핵 방향 중심력 → 퍼텐셜이 $r$에만 의존 → 구면좌표 자연스러움
- 파동함수는 $n$(주양자수), $l$(궤도), $m_l$에 따라 다른 방사형 분포
- Schrödinger 방정식은 $\psi$를 통해 상태 기술, $|\psi|^2$ = 확률밀도, **에너지도 이산적**

### 양자화 에너지
$$
E_n = -\frac{m e^4 Z^2}{8\varepsilon_0^2 h^2 n^2}, \qquad
\text{수소}(Z=1): \; E_1 = -13.6\,\text{eV}
$$
$n=1$ 전자를 무한히 떼려면 13.6 eV = **수소 이온화 에너지**. $n$ ↑ → $E \to 0$, 핵에 덜 구속.

**radial probability density** = $|\psi|^2 \cdot 4\pi r^2$ — $|\psi|^2$ 자체와 다름 (부피 요소 포함).

## 5. 궤도 각운동량

$$
|\vec{L}| = \sqrt{l(l+1)}\,\hbar, \qquad L_z = m_l\,\hbar
$$
크기와 자기장 방향 성분 $L_z$가 양자화 → 각운동량 벡터는 **허용된 특정 각도**만 (공간 양자화).

## 6. 광자 방출

들뜬 원자(높은 준위 전자) → 낮은 준위로 전이하며 광자 방출:
$$
h\nu = E_{high} - E_{low}
$$

**선택 규칙**: 모든 전이가 허용되는 게 아님 — 강의에서 강조: $\Delta l = \pm 1$ (전기 쌍극자 전이).

## 7. Laser 원리

| 과정 | 설명 |
|---|---|
| **Absorption** | 광자 흡수 → 낮은 준위 전자가 위로 |
| **Spontaneous emission** | 들뜬 원자가 임의 위상·방향으로 광자 방출 |
| **Stimulated emission** | 입사 광자와 **같은 에너지·위상·방향**의 광자 추가 방출 → 증폭·결맞음 |

### Population inversion
열평형에서는 낮은 준위 점유가 높음. laser 동작엔 **높은 준위에 더 많은 원자** 필요 → **metastable state**(전자가 오래 머묾)가 inversion을 가능하게 함. 3-level / 4-level 시스템.

### He–Ne laser
He가 방전으로 들뜸 → Ne에 에너지 전달 → Ne의 특정 준위 전이가 **632.8 nm** 붉은 laser. cavity mirror가 광자를 왕복시켜 stimulated emission 증폭, 일부가 출력 (한쪽 거울 부분 투과).

---

## 복습 질문

- $E < V_0$에서도 터널링 확률이 0이 아닌 이유, $T \approx e^{-2\alpha a}$에서 $\alpha$의 의존성은?
- STM 전류가 거리에 $e^{-2\alpha a}$로 의존한다는 것의 실용적 함의는?
- 흑체복사가 왜 고전 이론으로 설명 안 되고, Planck가 무엇을 도입했나?
- $|\psi|^2$와 radial probability density의 차이, 수소 이온화 에너지 13.6 eV의 의미는?
- absorption / spontaneous / stimulated emission의 차이, population inversion과 metastable state가 필요한 이유는?
{% endraw %}

---

이전: [04. 빛과 양자 이중성](04-light-and-quantum-duality.md) · 다음: [06. 전기·열 전도](06-electrical-and-thermal-conduction.md)
