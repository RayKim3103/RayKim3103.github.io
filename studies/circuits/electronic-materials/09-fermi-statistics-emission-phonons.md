---
layout: page
title: "09. Fermi Statistics Emission Phonons - 페르미 방출 포논"
permalink: /studies/circuits/electronic-materials/09-fermi-statistics-emission-phonons/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes/09%20Fermi%20Statistics%20Emission%20Phonons%20-%20%ED%8E%98%EB%A5%B4%EB%AF%B8%20%EB%B0%A9%EC%B6%9C%20%ED%8F%AC%EB%85%BC.md)

{% raw %}
tags: #ElectronicMaterials #FermiDirac #Thermoelectric #ThermionicEmission #FieldEmission #Phonon

이전: [Molecular Orbital and Energy Bands - 분자궤도 에너지밴드](08-molecular-orbital-and-energy-bands.md)  
다음: [Intrinsic Extrinsic Semiconductors - 반도체 캐리어](10-intrinsic-extrinsic-semiconductors.md)

## 핵심 요약

- Fermi-Dirac 통계는 Pauli 배타 원리를 따르는 전자의 에너지 점유 확률을 설명한다.
- 전자 농도는 density of states `g(E)`와 점유 확률 `f(E)`의 곱을 에너지에 대해 적분해 얻는다.
- 서로 다른 금속을 접촉시키면 Fermi level이 정렬될 때까지 전자가 이동하고 contact potential이 생긴다.
- Seebeck effect는 온도구배가 전위차를 만드는 thermoelectric 현상이다.
- thermionic emission은 열에너지가 work function을 넘는 전자 방출이고, field emission은 강한 전기장이 장벽을 얇게 만들어 일으키는 터널링 방출이다.
- phonon은 격자진동의 양자화된 에너지이며, 비금속 열전도의 주요 carrier다.

## Fermi-Dirac statistics

- 고체 내 전자는 서로 구별되지 않는 fermion이며 Pauli 배타 원리를 따른다.
- 한 quantum state에는 스핀까지 포함해 허용된 수만큼만 전자가 들어갈 수 있다.
- Fermi-Dirac 함수 `f(E)`는 에너지 `E` 상태가 전자로 점유될 확률이다.

```text
f(E) = 1 / [1 + exp((E - E_F) / kT)]
```

- 0 K에서는 `E < E_F` 상태가 채워지고 `E > E_F` 상태가 비어 있다.
- 온도가 올라가면 `E_F` 근처의 일부 전자가 더 높은 에너지 상태로 열적으로 들뜬다.

## Free electron model

- density of states `g(E)`는 단위 부피, 단위 에너지당 가능한 전자 상태 수다.
- 실제 전자 에너지 분포는 가능한 상태 수와 점유 확률의 곱으로 표현한다.

```text
n_E(E) = g(E) f(E)
n = integral n_E(E) dE
```

- `g(E)`가 커도 `f(E)`가 작으면 실제 전자 수는 작다.
- 반대로 `f(E)`가 1에 가까워도 가능한 상태가 없으면 전자는 존재할 수 없다.
- 반도체 conduction band의 전자 농도도 같은 논리로 계산한다.

## Metal-metal contact potential

- 서로 다른 금속을 접촉시키면 work function과 Fermi level 차이 때문에 전자가 한쪽 표면으로 이동한다.
- 전자 이동은 전하 분리를 만들고 contact potential `Delta V`를 형성한다.
- 평형에서는 두 금속의 Fermi level이 정렬된다.
- 두 금속으로 닫힌 회로를 만들면 두 접촉부의 contact potential이 서로 반대 방향으로 작용해 순전류가 흐르지 않는다.

## Seebeck effect와 thermocouple

### Seebeck effect

- 도체에 온도구배가 있으면 뜨거운 쪽의 전자가 더 높은 평균 에너지와 더 긴 평균 자유행로를 가져 차가운 쪽으로 확산한다.
- 이 전하 재분포가 전기장을 만들고 전위차가 생긴다.
- 전위차의 크기는 재료의 Seebeck coefficient와 온도차에 의해 결정된다.

```text
Delta V = S Delta T
```

### Thermocouple

- 같은 금속선만 사용해 Seebeck voltage를 측정하면 양쪽 접촉 효과가 상쇄되어 net emf가 0이 될 수 있다.
- 서로 다른 두 금속 A, B를 접합하면 두 재료의 Seebeck coefficient 차이 때문에 온도를 전압으로 측정할 수 있다.
- 한 접점은 reference temperature, 다른 접점은 측정 온도에 둔다.

## Thermionic emission

- 금속 내부 전자가 열적으로 높은 에너지를 얻어 `E_F + Phi` 이상이 되면 표면 장벽을 넘어 진공으로 방출될 수 있다.
- 온도가 증가하면 Fermi-Dirac tail이 높은 에너지 쪽으로 확장되어 방출 전자 수가 증가한다.
- vacuum tube와 CRT의 cathode emission은 thermionic emission의 대표 예다.

```text
emission requires electron energy > E_F + Phi
```

## Field emission

- 강한 전기장을 금속 표면에 걸면 표면 포텐셜 장벽이 얇아지고 삼각형에 가까운 장벽이 된다.
- 전자는 Fermi energy 근처에서도 얇아진 장벽을 터널링해 방출될 수 있다.
- 날카로운 tip에서는 전기장이 집중되므로 field emission이 쉽게 발생한다.

### Fowler-Nordheim emission

- Fowler-Nordheim field emission은 높은 전기장에서 전자 터널링 방출을 설명한다.
- Spindt tip cathode와 field emission display는 이 원리를 이용한다.
- Fowler-Nordheim plot은 방출 전류가 field emission 메커니즘을 따르는지 확인하는 데 쓰인다.

### CNT emitter

- carbon nanotube는 길고 매우 얇으며 끝이 뾰족해 국부 전기장 강화가 크다.
- 거의 이상적인 field emitter 형태를 갖기 때문에 FED, 전자원, 센서 응용에서 중요하다.

## Phonon

### 격자진동의 양자화

- 원자를 평형 위치 주변의 harmonic oscillator로 보면 가능한 진동 에너지가 양자화된다.
- 결정의 많은 원자가 결합되어 움직이면 lattice wave가 생긴다.
- 이 격자진동의 양자화된 입자적 표현이 phonon이다.

### Longitudinal wave와 transverse wave

- longitudinal wave: 원자 변위가 파동 진행 방향과 평행하다.
- transverse wave: 원자 변위가 파동 진행 방향과 수직이다.

## Phonon density of states와 heat capacity

- phonon DOS는 주파수별 phonon mode 수를 나타낸다.
- Debye approximation은 실제 phonon DOS를 단순화해 전체 mode 수가 맞도록 근사한다.
- Debye temperature `T_D`는 고체의 phonon spectrum과 heat capacity 거동을 특징짓는다.
- Si의 경우 `T_D = 625 K`로 제시되며, 300 K에서 `T/T_D = 0.48`이므로 molar heat capacity가 고전 한계 `3R`에 완전히 도달하지 않는다.

## 비금속 열전도와 phonon scattering

- 비금속에서는 자유전자가 부족하므로 phonon이 열을 운반한다.
- 뜨거운 영역에서 생성된 phonon은 차가운 영역으로 이동하며 열 에너지를 전달한다.
- phonon-phonon anharmonic interaction은 열 흐름 반대 방향의 phonon을 만들어 열전도를 제한할 수 있다.
- 결함, grain boundary, isotope disorder도 phonon을 산란시켜 열전도도를 낮춘다.

## 시험 포인트

- `g(E)`, `f(E)`, `g(E)f(E)`의 차이.
- 금속 접촉에서 Fermi level 정렬과 contact potential의 관계.
- Seebeck effect와 thermocouple의 작동 원리.
- thermionic emission과 field emission의 장벽 통과 방식 차이.
- sharp tip과 CNT가 field emission에 유리한 이유.
- phonon이 비금속 열전도의 carrier인 이유.
- Debye temperature와 heat capacity 곡선의 의미.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **09. Fermi Statistics Emission Phonons - 페르미 방출 포논**를 다루며, 원자 결합, 결정, 양자역학, band theory에서 반도체 소자 물성까지 이어지는 전자재료의 기초를 정리한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 거시적 전기 특성은 원자 결합, band structure, carrier statistics, scattering mechanism의 결과다.
- intrinsic/extrinsic semiconductor는 Fermi level 위치와 carrier concentration을 함께 보아야 한다.
- pn junction과 MOS 구조는 charge distribution, electric field, potential이 서로 적분/미분 관계로 연결된다.

### 문제 풀이 또는 구현 루틴

- energy band diagram을 먼저 그리고 Fermi level, carrier, field 방향을 표시한다.
- 전류는 drift, diffusion, generation-recombination 중 어떤 항이 지배적인지 나눈다.
- 소자 구조에서는 boundary condition과 depletion/accumulation/inversion 상태를 먼저 판별한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- electron energy와 electrostatic potential의 부호 관계를 혼동하기 쉽다.
- 평형 상태에서는 전류가 0이어도 drift와 diffusion이 각각 0이라는 뜻은 아니다.
- 도핑 농도 변화는 carrier 농도뿐 아니라 depletion width와 capacitance도 바꾼다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 현상은 band diagram에서 어떤 기울기나 장벽으로 보이는가?
- carrier 농도와 mobility 중 무엇이 conductivity를 지배하는가?
- 온도 변화가 Fermi level, carrier concentration, scattering에 어떤 영향을 주는가?
- **09. Fermi Statistics Emission Phonons - 페르미 방출 포논**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [08. Molecular Orbital and Energy Bands - 분자궤도 에너지밴드](08-molecular-orbital-and-energy-bands.md) · 다음: [10. Intrinsic Extrinsic Semiconductors - 반도체 캐리어](10-intrinsic-extrinsic-semiconductors.md)
