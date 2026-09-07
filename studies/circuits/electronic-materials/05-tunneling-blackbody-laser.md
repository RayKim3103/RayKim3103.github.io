---
layout: page
title: "05. Tunneling Blackbody Laser - 터널링 흑체 레이저"
permalink: /studies/circuits/electronic-materials/05-tunneling-blackbody-laser/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes/05%20Tunneling%20Blackbody%20Laser%20-%20%ED%84%B0%EB%84%90%EB%A7%81%20%ED%9D%91%EC%B2%B4%20%EB%A0%88%EC%9D%B4%EC%A0%80.md)

{% raw %}
tags: #ElectronicMaterials #QuantumTunneling #STM #Blackbody #Laser

이전: [Light and Quantum Duality - 빛과 양자 이중성](04-light-and-quantum-duality.md)  
다음: [Electrical and Thermal Conduction - 전기 열 전도](06-electrical-and-thermal-conduction.md)

## 핵심 요약

- 양자 터널링은 전자의 에너지 `E`가 장벽 높이 `V0`보다 작아도 장벽을 통과할 확률이 0이 아닌 현상이다.
- STM은 탐침과 시료 사이 거리 `a`에 지수적으로 민감한 터널링 전류를 이용해 원자 수준 표면 이미지를 얻는다.
- 흑체복사는 고전 이론의 한계를 드러냈고, 에너지 양자화 개념으로 이어졌다.
- Schrodinger 방정식의 파동함수는 전자의 확률분포를 나타내며, 원자 내 에너지는 양자화된다.
- Laser는 population inversion과 stimulated emission을 이용해 결맞은 빛을 증폭한다.

## 양자 터널링

### 고전적 장벽과 양자적 장벽

- 고전역학에서는 입자의 에너지가 장벽 높이보다 작으면 장벽을 넘을 수 없다.
- 양자역학에서는 전자를 파동함수로 표현하므로, 장벽 내부에서 파동함수가 지수적으로 감쇠하지만 완전히 0이 되지 않는다.
- 장벽이 얇으면 반대편에서 파동함수의 진폭이 남아 전자가 검출될 수 있다.

```text
inside barrier with E < V0: psi decays exponentially
tunneling probability increases when barrier is thinner or lower
```

### 터널링을 결정하는 직관

- 장벽 두께가 작을수록 통과 확률이 커진다.
- 전자 에너지가 장벽 높이에 가까울수록 통과 확률이 커진다.
- 유효질량이 작을수록 파동성 효과가 커져 터널링이 쉬워진다.

## Scanning Tunneling Microscope

- STM은 아주 날카로운 금속 tip과 도전성 시료 표면 사이에 bias를 걸고 터널링 전류를 측정한다.
- 터널링 전류는 tip과 표면 사이 거리 `a`에 대해 대략 다음처럼 의존한다.

```text
I_tunnel proportional to exp(-2 alpha a)
```

- 거리 변화가 작아도 전류가 크게 변하므로 원자 높이 차이를 매우 민감하게 감지한다.
- graphite 표면의 carbon ring이나 Ni(100) 표면 같은 원자 배열 이미징이 가능하다.
- STM은 전자 농도와 표면 상태를 함께 반영하므로 단순한 지형 측정 이상으로 해석해야 한다.

## 흑체복사

- 흑체는 들어오는 빛을 이상적으로 모두 흡수하고, 온도에 따라 특정 스펙트럼으로 복사하는 물체다.
- 온도가 높을수록 총 방출 에너지가 커지고, 스펙트럼 피크가 짧은 파장 쪽으로 이동한다.
- 흑체복사 문제는 에너지 교환이 연속적이라는 고전 가정으로 설명되지 않았고, Planck의 에너지 양자화로 해결되었다.

```text
photon energy: E = h nu
```

## 원자 내 전자 파동함수

### Hydrogen-like atom

- 전자는 양전하 핵 방향의 중심력을 받는다.
- 전자의 퍼텐셜 에너지는 핵에서의 거리 `r`에만 의존하므로 구면좌표가 자연스럽다.
- 파동함수는 주양자수와 궤도 양자수에 따라 다른 방사형 분포를 갖는다.

### Schrodinger 방정식의 물리적 의미

- 고전 방정식이 입자의 위치와 운동량을 직접 다룬다면, Schrodinger 방정식은 파동함수 `psi`를 통해 전자 상태를 기술한다.
- `|psi|^2`는 전자를 발견할 확률밀도다.
- 전자의 에너지 자체도 가능한 값들이 이산적으로 제한된다.

## 수소 원자의 양자화 에너지

```text
E_n = -m e^4 Z^2 / (8 epsilon_0^2 h^2 n^2)
For hydrogen, Z = 1
E_1 = -13.6 eV
```

- `n = 1`의 최저 에너지 상태에서 전자를 무한히 멀리 떼어내려면 13.6 eV가 필요하다.
- 이 값은 수소 원자의 이온화 에너지다.
- `n`이 커질수록 에너지는 0에 가까워지고, 전자는 핵에 덜 구속된다.

## 궤도 각운동량

- 전자의 orbital angular momentum은 크기와 외부 자기장 방향 성분이 양자화된다.
- 자기장 방향 성분 `L_z`는 `m_l` 값에 의해 결정된다.
- 각운동량 벡터는 허용된 특정 각도만 가질 수 있으며, 이는 공간 양자화를 의미한다.

## Photon emission

### 스펙트럼의 물리적 기원

- 원자가 충돌이나 광흡수로 들뜬 상태가 되면 높은 에너지 준위에 전자가 존재한다.
- 전자가 낮은 에너지 준위로 내려올 때 에너지 차이에 해당하는 photon을 방출한다.

```text
h nu = E_high - E_low
```

### 선택 규칙

- photon emission은 모든 전이가 가능한 것이 아니라 선택 규칙을 따른다.
- 강의에서는 허용 방출 과정의 조건으로 `Delta l = +/- 1`을 강조한다.

## Laser 원리

### 흡수, 자발방출, 유도방출

- absorption: photon을 흡수해 낮은 준위 전자가 높은 준위로 올라간다.
- spontaneous emission: 들뜬 원자가 임의의 위상과 방향으로 photon을 방출한다.
- stimulated emission: 들어온 photon과 같은 에너지, 위상, 방향의 photon을 추가로 방출한다.

### Population inversion

- 일반 열평형에서는 낮은 준위의 점유가 높다.
- laser 동작을 위해서는 높은 준위에 더 많은 원자를 모으는 population inversion이 필요하다.
- metastable state는 전자가 충분히 오래 머물러 inversion을 만들 수 있게 한다.

### He-Ne laser

- He 원자가 방전으로 들뜨고, Ne 원자에 에너지를 전달한다.
- Ne의 특정 준위 전이가 632.8 nm 부근의 붉은 laser 방출을 만든다.
- cavity mirror가 광자를 왕복시키며 stimulated emission을 증폭하고, 일부 빛이 출력으로 나온다.

## 시험 포인트

- `E < V0`에서도 터널링 확률이 0이 아닌 이유.
- STM 전류가 `exp(-2 alpha a)`에 비례한다는 거리 민감도.
- 흑체복사가 photon energy `h nu` 개념으로 이어지는 이유.
- `|psi|^2`와 radial probability density의 차이.
- 수소 원자 이온화 에너지 13.6 eV의 의미.
- Laser에서 metastable state, population inversion, stimulated emission의 역할.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **05. Tunneling Blackbody Laser - 터널링 흑체 레이저**를 다루며, 원자 결합, 결정, 양자역학, band theory에서 반도체 소자 물성까지 이어지는 전자재료의 기초를 정리한다.
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
- **05. Tunneling Blackbody Laser - 터널링 흑체 레이저**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04. Light and Quantum Duality - 빛과 양자 이중성](04-light-and-quantum-duality.md) · 다음: [06. Electrical and Thermal Conduction - 전기 열 전도](06-electrical-and-thermal-conduction.md)
