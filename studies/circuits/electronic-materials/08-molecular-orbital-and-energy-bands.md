---
layout: page
title: "08. Molecular Orbital and Energy Bands - 분자궤도 에너지밴드"
permalink: /studies/circuits/electronic-materials/08-molecular-orbital-and-energy-bands/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes/08%20Molecular%20Orbital%20and%20Energy%20Bands%20-%20%EB%B6%84%EC%9E%90%EA%B6%A4%EB%8F%84%20%EC%97%90%EB%84%88%EC%A7%80%EB%B0%B4%EB%93%9C.md)

{% raw %}
tags: #ElectronicMaterials #MolecularOrbital #EnergyBand #EffectiveMass

이전: [Silicon Thin Film Crystallization - Si 박막 결정화](07-silicon-thin-film-crystallization-si.md)  
다음: [Fermi Statistics Emission Phonons - 페르미 방출 포논](09-fermi-statistics-emission-phonons.md)

## 핵심 요약

- 원자 궤도가 겹치면 bonding orbital과 antibonding orbital로 갈라진다.
- 많은 원자가 고체를 이루면 갈라진 에너지 준위들이 매우 촘촘해져 energy band가 된다.
- 금속은 부분적으로 찬 밴드 또는 겹친 밴드를 가져 전기전도가 쉽다.
- Si는 sp3 hybrid orbital이 결합 orbital과 반결합 orbital을 만들고, 이들이 각각 valence band와 conduction band로 확장된다.
- 결정 안의 전자는 외부 힘에 대한 응답이 자유공간 전자와 달라 effective mass `m*`로 표현한다.

## Hydrogen molecule의 molecular orbital

- 두 H 원자가 가까워지면 각 원자의 1s orbital이 겹친다.
- 겹침 방식에 따라 bonding orbital `psi_sigma`와 antibonding orbital `psi_sigma*`가 생긴다.
- 두 전자는 스핀을 짝지어 낮은 에너지의 bonding orbital을 채운다.
- bonding orbital을 채우면 원자 사이 전자밀도가 증가하고 핵-전자 인력이 커져 분자가 안정해진다.

## Hydrogen과 Helium 비교

### H2

- H 원자 두 개는 각각 1개의 1s 전자를 갖는다.
- 두 전자가 bonding orbital에 들어가면 전체 에너지가 낮아져 안정한 H2 분자가 된다.

### He2가 안정하지 않은 이유

- He 원자는 1s orbital이 이미 가득 차 있다.
- 두 He 원자가 가까워지면 1s 준위가 bonding과 antibonding으로 갈라지지만, 전자들이 두 준위를 모두 채운다.
- bonding에서 얻는 안정화와 antibonding에서 생기는 불안정화가 상쇄되거나 전체 에너지를 높여 안정 결합을 만들지 못한다.
- 강의의 핵심 직관은 결합 형성에는 H2처럼 half-occupied orbital의 겹침이 유리하다는 점이다.

## Energy band formation

### 소수 원자에서 다수 원자로

- 원자 2개가 만나면 한 준위가 두 준위로 갈라진다.
- 원자 3개가 만나면 세 가지 molecular orbital이 생긴다.
- 원자 수 `N`이 매우 커지면 갈라진 준위 간격이 매우 작아져 거의 연속적인 band처럼 보인다.

### Li solid 예시

- N개의 Li 원자가 모이면 N개의 2s 전자가 존재한다.
- 2s orbital에서 만들어진 band에는 spin까지 고려해 2N개의 state가 있다.
- N개의 전자가 2N개 state를 채우므로 2s band는 half full이다.
- half-filled band는 전기장에 의해 전자가 빈 상태로 쉽게 이동할 수 있어 금속 전도성을 설명한다.

## 원자 궤도 겹침과 band

- 고체 원자들이 무한히 멀리 떨어져 있다면 각 원자는 고유한 discrete atomic level을 갖는다.
- 원자가 가까워질수록 바깥쪽 orbital부터 겹치며 band를 형성한다.
- 3s orbital은 3s band, 2p orbital은 2p band처럼 확장된다.
- 여러 band가 서로 겹치면 전자가 차지할 수 있는 에너지가 거의 연속처럼 이어진다.

## 금속의 energy band

### 부분적으로 찬 밴드

- 금속에서는 valence electron이 들어 있는 band가 부분적으로만 차 있다.
- Fermi level 근처에 빈 state가 있으므로 전기장이 걸리면 전자가 쉽게 에너지를 바꾸고 drift한다.
- 진공 준위는 전자가 고체에서 완전히 벗어난 상태의 기준이다.

### 전기장과 밴드 기울어짐

- 전압을 걸면 전자의 electrostatic potential energy가 위치에 따라 달라져 energy band가 공간적으로 기울어진다.
- 전자는 양전위 쪽으로 이동할 때 potential energy가 낮아진다.
- 전류는 Fermi level 근처 전자의 비대칭 점유가 만들어내는 net motion으로 이해한다.

## Si의 전자 구조

### Isolated Si atom

- Si는 Group IV 원소이며 원자가전자는 3s 전자 2개와 3p 전자 2개다.
- 결합 전에는 3s와 3p orbital이 서로 다른 에너지를 갖는다.

### sp3 hybridization

- 결합 직전 3s orbital 하나와 3p orbital 세 개가 섞여 네 개의 동일한 sp3 hybrid orbital을 만든다.
- 네 hybrid orbital은 사면체 꼭짓점 방향으로 향하며 각도는 약 109.5도다.
- 각 hybrid orbital에는 원자가전자 하나가 들어가 이웃 Si와 공유결합을 형성할 수 있다.

## Si crystal의 band formation

- 이웃한 Si 원자의 sp3 hybrid orbital이 겹치면 bonding orbital `psi_B`와 antibonding orbital `psi_A`가 생긴다.
- 결정 전체에서 bonding orbital들이 겹쳐 valence band를 형성한다.
- antibonding orbital들이 겹쳐 conduction band를 형성한다.
- 0 K에서 valence band는 가득 차고 conduction band는 비어 있다.
- 두 band 사이의 gap이 Si의 semiconductor 성질을 만든다.

## Semiconductor energy band

- conduction band `CB`: 전자가 이동하며 전류에 기여할 수 있는 band.
- valence band `VB`: 결합 전자가 주로 채우는 band.
- band gap `E_g`: VB top과 CB bottom 사이의 금지 에너지 영역.
- 0 K에서는 전자가 VB를 모두 채워도 빈 상태가 없어 전류가 흐르기 어렵다.
- 유한 온도나 빛에 의해 일부 전자가 CB로 올라가면 electron-hole pair가 생기고 전도성이 나타난다.

## Effective mass

- 자유공간 전자는 외부 힘에 대해 `a = F / m_e`로 가속된다.
- 결정 속 전자는 주기적 퍼텐셜, band curvature, 산란의 영향을 받으므로 자유전자 질량이 아닌 effective mass `m*`를 사용한다.

```text
a_crystal = F_ext / m*
```

- `m*`는 실제 전자 질량이 바뀐다는 뜻이 아니라 결정 내 band structure가 전자의 가속 응답을 바꾼다는 모델 파라미터다.
- band curvature가 클수록 일반적으로 effective mass가 작고 mobility가 커지기 쉽다.

## 시험 포인트

- bonding orbital과 antibonding orbital의 에너지 차이.
- H2는 결합하지만 He2가 안정하지 않은 이유.
- N개 원자가 모일 때 discrete level이 band가 되는 과정.
- Li의 half-filled 2s band가 금속성을 설명하는 방식.
- Si의 sp3 hybridization과 valence band, conduction band 형성.
- effective mass가 필요한 이유와 `m*`의 물리적 의미.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **08. Molecular Orbital and Energy Bands - 분자궤도 에너지밴드**를 다루며, 원자 결합, 결정, 양자역학, band theory에서 반도체 소자 물성까지 이어지는 전자재료의 기초를 정리한다.
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
- **08. Molecular Orbital and Energy Bands - 분자궤도 에너지밴드**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [07. Silicon Thin Film Crystallization - Si 박막 결정화](07-silicon-thin-film-crystallization-si.md) · 다음: [09. Fermi Statistics Emission Phonons - 페르미 방출 포논](09-fermi-statistics-emission-phonons.md)
