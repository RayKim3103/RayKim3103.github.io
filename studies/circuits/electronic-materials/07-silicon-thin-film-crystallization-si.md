---
layout: page
title: "07. Silicon Thin Film Crystallization - Si 박막 결정화"
permalink: /studies/circuits/electronic-materials/07-silicon-thin-film-crystallization-si/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes/07%20Silicon%20Thin%20Film%20Crystallization%20-%20Si%20%EB%B0%95%EB%A7%89%20%EA%B2%B0%EC%A0%95%ED%99%94.md)

{% raw %}
tags: #ElectronicMaterials #SiliconThinFilm #LTPS #ELA #TFT

이전: [Electrical and Thermal Conduction - 전기 열 전도](06-electrical-and-thermal-conduction.md)  
다음: [Molecular Orbital and Energy Bands - 분자궤도 에너지밴드](08-molecular-orbital-and-energy-bands.md)

## 핵심 요약

- Si 박막 결정화는 비정질 Si를 poly-Si로 바꾸어 TFT 이동도와 전류 구동 능력을 높이는 공정이다.
- 대표 방법에는 furnace anneal, SPC, RTA, CGS, MILC, ELA, SLS가 있다.
- 생산 공정에서는 특히 ELA와 SLS가 중요하게 다루어진다.
- ELA는 excimer laser로 표면 a-Si만 빠르게 녹이고 재결정화해 glass substrate 손상을 줄인다.
- grain boundary는 TFT의 threshold voltage 상승, off current 증가, mobility 저하를 일으킨다.
- laser energy density는 partial melting, complete melting, super lateral growth를 결정한다.

## Si 박막 결정화 방법

| 방법 | 의미 | 핵심 특징 |
|---|---|---|
| Furnace anneal | 노 전체 가열 | 긴 시간, 높은 thermal budget |
| SPC | solid phase crystallization | 고상 상태에서 결정화 |
| RTA | rapid thermal annealing | 짧은 시간 고온 열처리 |
| CGS | continuous grain Si | 연속 grain 형성 |
| MILC | metal induced lateral crystallization | 금속 촉매로 lateral crystallization 유도 |
| ELA | excimer laser annealing | 표면만 순간 용융 후 재결정화 |
| SLS | sequential lateral solidification | lateral growth를 순차적으로 제어 |

## Rapid Thermal Annealing

- RTA는 dopant activation과 metal contact의 interfacial reaction에 쓰이는 반도체 공정이다.
- wafer를 상온에서 약 1000-1500 K까지 빠르게 가열한다.
- 목표 온도에서 몇 초만 유지한 뒤 빠르게 냉각한다.
- 긴 furnace anneal보다 diffusion broadening을 줄이면서 필요한 열처리 효과를 얻을 수 있다.

## Excimer Laser Annealing

### 공정 단계

1. glass substrate 위에 amorphous silicon layer를 증착한다.
2. pulsed rectangular UV laser beam을 스캔한다.
3. a-Si 표면층이 빠르게 녹는다.
4. 냉각 중 재결정화되어 poly-Si가 된다.
5. laser 에너지는 주로 표면 a-Si에서 흡수되므로 glass substrate는 상대적으로 영향을 덜 받는다.

### ELA의 장점과 문제

- 장점: 결정화 시간이 매우 짧고, 저온 기판에서도 poly-Si 형성이 가능하다.
- 장점: 여러 shot과 overlap 조건을 이용해 grain size를 키울 수 있다.
- 문제: OLED 또는 large-area display에서 균일도 문제가 생기기 쉽다.
- 예시 조건으로 95% overlap, 20 shots on a-Si 같은 다중 조사 조건이 다뤄진다.

## Polycrystalline Silicon TFT

### Poly-Si의 의미

- poly-Si는 여러 결정 grain이 모인 Si 박막이다.
- grain 내부는 결정질에 가깝지만, grain boundary에서는 결합 불완전성과 defect state가 많다.
- a-Si보다 carrier conduction이 좋아 TFT 성능을 높일 수 있지만, grain boundary 품질이 성능을 제한한다.

### Grain boundary 효과

- threshold voltage `V_th`가 증가할 수 있다.
- off current `I_off`가 증가할 수 있다.
- carrier mobility가 감소한다.
- trap state가 많아 transfer curve의 subthreshold 특성이 나빠질 수 있다.

### TFT transfer curve

- transfer curve는 drain voltage 조건을 고정하고 gate voltage를 변화시키며 drain current를 측정한 곡선이다.
- n-type TFT와 p-type TFT는 carrier 종류와 mobility 차이 때문에 전류 수준과 기울기가 다르다.

## ELA에서 energy density의 역할

### 분석 방법

- TEM은 ex-situ로 grain 구조를 관찰한다.
- TR, transient reflectance는 in-situ로 laser 조사 중 melting과 solidification dynamics를 본다.
- energy density에 따른 average grain radius와 melt duration을 비교하면 결정화 regime을 판단할 수 있다.

### 세 가지 결정화 regime

| Energy density | 상태 | 결과 grain |
|---|---|---|
| 낮음 | partial melting | vertical regrowth, small grains |
| 너무 높음 | complete melting | copious nucleation, fine grains |
| 적절함 | near complete melting | super lateral growth, large grains |

## Super Lateral Growth

- SLG는 거의 완전히 녹은 영역에서 남은 seed를 중심으로 lateral growth가 길게 진행되는 regime이다.
- seed가 가까우면 lateral growth가 서로 만나 continuous large-grained poly-Si를 만든다.
- seed가 너무 멀면 완전 용융 영역에서 copious nucleation이 먼저 발생해 isolated disk 또는 fine grain이 생길 수 있다.
- 따라서 energy density, pulse overlap, seed spacing 제어가 grain size와 균일도에 직접 연결된다.

## Multiple Pulse Irradiation

- 여러 번 laser pulse를 조사하면 melt-mediated grain growth가 반복되어 grain enlargement가 일어날 수 있다.
- 하지만 pulse마다 국부 용융과 재고화가 반복되므로 공정 윈도우가 좁고, 균일도 확보가 중요하다.

## Phase Transformation

### Heating

- laser pulse가 들어오면 a-Si의 온도가 급격히 상승한다.
- energy density가 충분하면 표면층이 녹고, 용융 깊이는 laser fluence와 흡수율에 따라 달라진다.

### Cooling

- pulse 이후 열이 기판과 주변으로 빠져나가며 재고화가 진행된다.
- 냉각 속도와 seed 존재 여부가 nucleation과 growth의 경쟁을 결정한다.
- recalescence는 결정화 중 방출되는 잠열 때문에 온도 변화가 일시적으로 완만해지는 현상으로 이해할 수 있다.

## Grain Boundary 위치 제어

- TFT channel 내부에 grain boundary가 놓이면 carrier 이동 경로가 trap과 barrier를 만나 성능 편차가 커진다.
- SLS나 artificially controlled SLG는 grain boundary 위치를 channel 밖으로 유도해 device uniformity를 높이려는 접근이다.
- source, drain, gate 배치와 grain boundary 위치의 관계가 TFT 특성 최적화에서 중요하다.

## 시험 포인트

- RTA와 ELA의 공정 차이.
- ELA가 glass substrate를 크게 손상시키지 않는 이유.
- grain boundary가 TFT의 `V_th`, `I_off`, mobility에 주는 영향.
- partial melting, complete melting, SLG regime의 차이.
- TR analysis로 melt duration과 energy density 관계를 보는 이유.
- grain boundary 위치 제어가 TFT 균일도에 중요한 이유.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **07. Silicon Thin Film Crystallization - Si 박막 결정화**를 다루며, 원자 결합, 결정, 양자역학, band theory에서 반도체 소자 물성까지 이어지는 전자재료의 기초를 정리한다.
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
- **07. Silicon Thin Film Crystallization - Si 박막 결정화**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [06. Electrical and Thermal Conduction - 전기 열 전도](06-electrical-and-thermal-conduction.md) · 다음: [08. Molecular Orbital and Energy Bands - 분자궤도 에너지밴드](08-molecular-orbital-and-energy-bands.md)
