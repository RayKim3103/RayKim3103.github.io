---
layout: page
title: "09. 페르미 통계·전자 방출·포논 (Fermi Statistics / Emission / Phonons)"
permalink: /studies/circuits/electronic-materials/09-fermi-statistics-emission-phonons/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes) · 교재: Kasap Ch.4
- 이전: [08. 분자궤도와 에너지밴드](08-molecular-orbital-and-energy-bands.md) · 다음: [10. 반도체 캐리어](10-intrinsic-extrinsic-semiconductors.md)

{% raw %}
## 개요

```text
Fermi–Dirac f(E) → n = ∫ g(E)f(E) dE
금속 접촉 → Fermi level 정렬 + contact potential → Seebeck / thermocouple
thermionic emission (열) vs field emission (강한 전기장, 터널링, Fowler–Nordheim, CNT)
phonon = 격자진동의 양자 → 비금속 열전도, Debye
```

---

## 1. Fermi–Dirac 통계

전자는 구별 불가 fermion, Pauli 배타 원리. 에너지 $E$ 상태가 점유될 확률:
$$
f(E) = \frac{1}{1 + \exp\!\big((E - E_F)/kT\big)}
$$
- 0 K: $E < E_F$ 채워짐, $E > E_F$ 비어 있음
- $T > 0$: $E_F$ 근처 전자가 열적으로 들뜸 (thermal tail, 폭 $\sim kT$)
- $E - E_F \gg kT$ 이면 $f(E) \approx e^{-(E-E_F)/kT}$ (Boltzmann 근사)

## 2. Free Electron Model — 전자 농도

**Density of states** $g(E)$ = 단위 부피·단위 에너지당 가능한 상태 수 ($\propto \sqrt{E}$ for free electron).
$$
n_E(E) = g(E)\,f(E), \qquad n = \int g(E)\,f(E)\,dE
$$
- $g(E)$ 커도 $f(E)$ 작으면 전자 수 작음
- $f(E) \approx 1$ 이어도 상태 없으면 전자 없음
- 반도체 CB 전자 농도도 같은 논리 ($n = N_C e^{-(E_C - E_F)/kT}$).

## 3. Metal–Metal Contact Potential

서로 다른 금속 접촉 → work function·$E_F$ 차이로 전자가 한쪽으로 이동 → 전하 분리 → **contact potential $\Delta V$**. 평형에서 **두 금속의 $E_F$ 정렬**. 닫힌 회로(같은 두 금속)면 두 접촉부 contact potential이 상쇄 → 순전류 0.

## 4. Seebeck Effect와 Thermocouple

### Seebeck
온도구배가 있으면 뜨거운 쪽 전자가 더 높은 평균 에너지·긴 자유행로 → 차가운 쪽으로 확산 → 전하 재분포 → 전기장·전위차:
$$
\Delta V = S\,\Delta T
$$
$S$ = Seebeck coefficient (재료 고유).

### Thermocouple
같은 금속선만 쓰면 양쪽 접촉 효과 상쇄 → net emf 0. **서로 다른 두 금속 A, B** 접합 → $S_A - S_B$ 차이로 온도를 전압으로 측정. 한 접점 = reference, 다른 접점 = 측정 온도.

## 5. Thermionic Emission

금속 내부 전자가 열적으로 $E_F + \Phi$ 이상 에너지를 얻으면 표면 장벽을 넘어 진공 방출.
$$
J = A_0 T^2 e^{-\Phi/kT} \quad (\text{Richardson–Dushman})
$$
온도 ↑ → Fermi–Dirac tail 확장 → 방출 전자 수 급증. vacuum tube·CRT cathode.

## 6. Field Emission

강한 전기장을 표면에 걸면 포텐셜 장벽이 **얇아지고 삼각형에 가까워짐** → Fermi energy 근처 전자도 **터널링** 방출.
- **Fowler–Nordheim emission**: $\;J \propto E^2 \exp(-B/E)$; Fowler–Nordheim plot($\ln(J/E^2)$ vs $1/E$)이 직선이면 FN 메커니즘.
- **날카로운 tip**: 전기장 집중 → field emission 쉬움. Spindt tip cathode, FED.
- **CNT emitter**: 길고 얇고 뾰족 → 국부 전기장 강화 큼 → 거의 이상적 field emitter (FED, 전자원, 센서).

## 7. Phonon

### 격자진동의 양자화
원자를 평형 위치 주변 harmonic oscillator로 → 진동 에너지 양자화. 많은 원자가 결합해 움직이면 **lattice wave** → 그 양자적 입자 표현이 **phonon** ($E = \hbar\omega$, 운동량 $\hbar q$).

- **Longitudinal wave**: 변위 ∥ 진행 방향
- **Transverse wave**: 변위 ⊥ 진행 방향

### Phonon DOS와 heat capacity
- phonon DOS = 주파수별 mode 수. **Debye approximation**은 실제 DOS를 단순화(선형 분산 + cutoff)해 전체 mode 수를 맞춤.
- **Debye temperature $T_D$**가 phonon spectrum·heat capacity 거동을 특징지음.
- **Si: $T_D = 625\,\text{K}$**. 300 K에서 $T/T_D = 0.48$ → molar heat capacity가 고전 한계 $3R$에 완전히 도달하지 못함 (Dulong–Petit 미달).

## 8. 비금속 열전도와 Phonon Scattering

- 자유전자 부족 → phonon이 열 운반. 뜨거운 영역 phonon이 차가운 영역으로 이동.
- **Phonon–phonon anharmonic (Umklapp) interaction** → 열 흐름 반대 방향 phonon 생성 → 열전도 제한.
- 결함, grain boundary, isotope disorder도 phonon 산란 → $\kappa$ ↓.

---

## 복습 질문

- $g(E)$, $f(E)$, $g(E)f(E)$의 차이와, 전자 농도를 구하는 적분식은?
- 금속 접촉에서 $E_F$ 정렬과 contact potential의 관계, thermocouple이 왜 서로 다른 두 금속을 쓰나?
- thermionic emission과 field emission의 장벽 통과 방식 차이, sharp tip·CNT가 field emission에 유리한 이유는?
- phonon이 비금속 열전도의 carrier인 이유, Umklapp scattering이 열전도를 제한하는 방식은?
- Si의 $T_D = 625\,\text{K}$에서 300 K heat capacity가 $3R$에 도달 못 하는 이유는?
{% endraw %}

---

이전: [08. 분자궤도와 에너지밴드](08-molecular-orbital-and-energy-bands.md) · 다음: [10. 반도체 캐리어](10-intrinsic-extrinsic-semiconductors.md)
