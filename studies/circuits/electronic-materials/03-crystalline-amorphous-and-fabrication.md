---
layout: page
title: "03. Crystalline Amorphous and Fabrication - 결정질 비정질 공정"
permalink: /studies/circuits/electronic-materials/03-crystalline-amorphous-and-fabrication/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes/03%20Crystalline%20Amorphous%20and%20Fabrication%20-%20%EA%B2%B0%EC%A0%95%EC%A7%88%20%EB%B9%84%EC%A0%95%EC%A7%88%20%EA%B3%B5%EC%A0%95.md)

{% raw %}
tags: #ElectronicMaterials #Amorphous #Czochralski #PVD #CVD #Interconnect

이전: [Crystal Structure and Defects - 결정구조와 결함](02-crystal-structure-and-defects.md)  
다음: [Light and Quantum Duality - 빛과 양자 이중성](04-light-and-quantum-duality.md)

## 핵심 요약

- 고체는 결정질, 비정질, 다결정으로 나눌 수 있다.
- 결정질은 장거리 질서가 있고, 비정질은 단거리 질서만 가지며, 다결정은 서로 다른 방향의 작은 grain들이 모인 구조다.
- 비정질 Si는 dangling bond와 localized state 때문에 결정 Si와 전기적 성질이 다르며, 수소화 a-Si:H에서는 H가 dangling bond를 passivation한다.
- 단결정 Si는 Czochralski 방법으로 성장하고, 비정질 막은 급랭, PVD, CVD 등으로 만들 수 있다.
- 미세전자 배선에서는 다층 금속 interconnect와 via가 핵심이며, electromigration은 고신뢰성 설계의 중요한 실패 메커니즘이다.

## 고체의 세 가지 상태

| 상태 | 구조 | 주요 특징 |
|---|---|---|
| crystalline solid | 원자가 주기적으로 배열 | 장거리 질서, 뚜렷한 결정면과 방향성 |
| amorphous solid | 원자가 무작위에 가깝게 배열 | 장거리 질서 없음, 유리 전이 특성 |
| polycrystalline solid | 작은 결정립들이 서로 다른 방향으로 배열 | grain boundary 존재, 국소적으로만 결정성 |

### 결정질과 비정질의 차이

- 결정질 SiO2는 장거리 질서를 가지며 밀도가 상대적으로 높다.
- 비정질 silica는 단거리 결합 구조는 유지하지만 장거리 반복성이 없다.
- 비정질 구조에는 void, strained bond, dangling bond가 많아 전자 상태가 band gap 내부로 들어올 수 있다.

## 결정질 Si, a-Si, a-Si:H

### 결정질 Si

- Si 원자는 sp3 공유결합으로 네 이웃과 사면체 결합을 이룬다.
- 주기적인 결정 퍼텐셜 덕분에 잘 정의된 valence band와 conduction band가 형성된다.

### 비정질 Si

- 장거리 질서가 없고, dangling bond와 void가 존재한다.
- band edge 근처에 localized state가 생기며, carrier 이동도가 낮아진다.
- dangling bond는 recombination center나 trap으로 작용할 수 있다.

### 수소화 비정질 Si

- a-Si:H에서는 수소가 dangling bond를 포화시켜 결함 상태 밀도를 줄인다.
- 박막 태양전지와 디스플레이 소자에서 활용된다.

## Grain과 grain boundary

- 다결정 고체는 핵생성 후 성장한 grain들이 서로 만나며 형성된다.
- grain마다 결정 방향이 다르므로 경계에서는 원자가 자연스러운 결합 구조를 유지하기 어렵다.
- grain boundary에는 broken bond, vacancy, void, strained bond, interstitial-type atom이 존재한다.
- grain boundary 원자는 grain 내부 원자보다 높은 에너지를 가지며, 전하 trap과 산란 중심으로 작동한다.

## 표면과 화학양론

### 결정 표면

- 표면 원자는 bulk와 달리 모든 결합을 채우지 못해 dangling bond를 갖는다.
- 표면 원자들은 dangling bond를 줄이기 위해 reconstruction을 일으킬 수 있다.
- 표면에는 physisorption과 chemisorption이 일어나며, 이는 박막 성장과 계면 특성을 바꾼다.

### Stoichiometry와 non-stoichiometry

- stoichiometric compound는 양이온과 음이온이 정확한 비율로 존재하는 화합물이다.
- non-stoichiometric compound는 한 성분이 과잉으로 존재하거나 결함으로 인해 이상 조성에서 벗어난다.
- 예를 들어 ZnO에서 과잉 Zn이 interstitial Zn2+로 들어가면 자유전자와 결함 구조가 생길 수 있다.

## Czochralski 단결정 성장

### 공정 흐름

1. 고순도 polysilicon을 용융한다.
2. 원하는 dopant를 넣어 전기적 특성을 조절한다.
3. seed crystal을 용융 Si에 접촉시킨다.
4. seed를 회전시키며 서서히 끌어올려 단결정 ingot을 성장시킨다.
5. ingot을 절단, 연마하여 wafer를 만든다.

### 핵심 특징

- Si ingot 내부 원자는 diamond cubic lattice에 맞춰 주기적으로 배열된다.
- ingot 길이는 매우 길 수 있고, wafer 두께는 일반적으로 수백 micrometer 수준이다.
- wafer의 결정 방향은 flat 또는 notch로 표시한다.

## 비정질 제조

### 냉각 속도와 상 변화

- 느린 냉각: 원자가 충분히 재배열되어 결정질 고체가 된다.
- 빠른 냉각, quenching: 결정화할 시간이 부족해 supercooled liquid가 amorphous solid로 굳는다.
- 결정질 전이는 뚜렷한 melting temperature `T_m`을 보이지만, 비정질은 glass transition temperature `T_g`를 중심으로 연속적 변화가 나타난다.

### Melt spinning

- 용융 금속 합금을 빠르게 냉각해 crystallization을 우회하고 metallic glass를 만든다.
- 급랭 속도가 충분히 빠르면 원자 배열이 장거리 질서를 만들기 전에 고정된다.

### PVD

- e-beam evaporation: 고융점 Si를 전자빔으로 국부 가열해 증발시키고, 기판 위에 응축시켜 a-Si 막을 만든다.
- sputtering: plasma에서 가속된 ion이 target을 때려 target atom을 방출시키고, 방출 원자가 substrate에 증착된다.

### CVD

- a-Si:H는 RF plasma에서 silane 분자가 분해되어 Si와 H 원자가 기판에 응축하면서 성장한다.
- 수소 포함은 dangling bond passivation에 중요하다.

## 고용체와 상평형

- solid solution은 한 상 안에서 조성, 구조, 성질이 균일한 합금이다.
- substitutional solid solution은 host 자리를 다른 원자가 대체한다.
- interstitial solid solution은 작은 원자가 host atom 사이 빈 공간에 들어간다.
- Cu-Ni isomorphous alloy는 냉각 조건에 따라 균일 조성 또는 grain 내부 segregation을 보일 수 있다.
- 빠른 냉각에서는 먼저 고화된 영역과 나중에 고화된 영역의 조성이 달라져 grain boundary 근처 조성 편차가 생긴다.

## Zone refining

- zone refining은 고순도 결정 제조에 쓰인다.
- 국부적으로 녹인 영역을 이동시키면 불순물이 액상 쪽에 더 많이 남고, 재고화된 고체의 불순물 농도는 낮아진다.
- 여러 번 반복하면 불순물이 한쪽 끝으로 몰려 전체 결정 순도가 향상된다.

## Microelectronic interconnect

- 집적회로에서는 M1, M2, M3 등 여러 금속 배선층과 via가 소자를 연결한다.
- 배선 주변 dielectric과 배선 사이에는 수직 capacitance `C_V`와 수평 capacitance `C_H`가 생긴다.
- 미세화될수록 RC delay, crosstalk, electromigration 문제가 중요해진다.

### Electromigration

- 높은 전류밀도에서 전자가 금속 이온에 운동량을 전달해 이온이 서서히 이동하는 현상이다.
- grain boundary와 interface를 따라 void와 hillock이 형성될 수 있다.
- void가 커지면 배선이 끊어지고, hillock은 단락이나 신뢰성 저하를 유발한다.

## 시험 포인트

- 결정질, 비정질, 다결정의 구조 차이.
- a-Si와 a-Si:H에서 dangling bond와 localized state의 의미.
- Czochralski 공정 순서와 단결정 wafer 제조 원리.
- quenching, melt spinning, PVD, CVD의 역할 차이.
- solid solution과 phase diagram이 조성 균일성에 주는 의미.
- electromigration이 금속 interconnect 실패를 일으키는 과정.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. Crystalline Amorphous and Fabrication - 결정질 비정질 공정**를 다루며, 원자 결합, 결정, 양자역학, band theory에서 반도체 소자 물성까지 이어지는 전자재료의 기초를 정리한다.
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
- **03. Crystalline Amorphous and Fabrication - 결정질 비정질 공정**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. Crystal Structure and Defects - 결정구조와 결함](02-crystal-structure-and-defects.md) · 다음: [04. Light and Quantum Duality - 빛과 양자 이중성](04-light-and-quantum-duality.md)
