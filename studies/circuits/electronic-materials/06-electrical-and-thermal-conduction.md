---
layout: page
title: "06. Electrical and Thermal Conduction - 전기 열 전도"
permalink: /studies/circuits/electronic-materials/06-electrical-and-thermal-conduction/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes/06%20Electrical%20and%20Thermal%20Conduction%20-%20%EC%A0%84%EA%B8%B0%20%EC%97%B4%20%EC%A0%84%EB%8F%84.md)

{% raw %}
tags: #ElectronicMaterials #Conduction #Resistivity #HallEffect #ThermalConductivity

이전: [Tunneling Blackbody Laser - 터널링 흑체 레이저](05-tunneling-blackbody-laser.md)  
다음: [Silicon Thin Film Crystallization - Si 박막 결정화](07-silicon-thin-film-crystallization-si.md)

## 핵심 요약

- 전기전도는 자유롭게 이동 가능한 carrier와 전기장에 의한 drift로 설명한다.
- 금속은 페르미 준위 근처 전자의 drift가 전류를 만들고, 반도체는 전자와 정공이 모두 전도에 참여한다.
- 저항률은 열진동, 불순물, grain boundary, dislocation 같은 산란 메커니즘의 합으로 증가한다.
- Hall effect는 carrier type, carrier concentration, mobility를 판단하는 핵심 측정법이다.
- 금속의 열전도와 전기전도는 Wiedemann-Franz-Lorenz 법칙으로 연결된다.

## Conduction electron

- 금속에서는 전도 전자가 이미 부분적으로 찬 밴드 안에 존재하며, 작은 전기장에도 이동할 수 있다.
- 반도체에서는 전도대의 전자와 가전자대의 정공이 carrier가 된다.
- 도체는 자유전자가 내부에서 비교적 자유롭게 이동할 수 있는 물질이다.

## Drift 전도

### 전류밀도

- 전류밀도는 단위 면적을 단위 시간에 통과하는 전하량이다.
- 전자 농도 `n`, 전하량 `q`, drift velocity `v_d`가 전류밀도를 정한다.

```text
J = n q v_d
```

- 전자는 음전하이므로 전자 drift 방향과 관습적 전류 방향은 반대다.

### 전기장이 없을 때와 있을 때

- 전기장이 없을 때 전자는 열운동으로 무작위 운동을 하지만 평균 drift는 0이다.
- 전기장이 걸리면 무작위 열운동 위에 작은 평균 drift velocity가 생겨 순전류가 흐른다.
- 전자의 평균 자유행로 `l`은 충돌 사이 평균 이동거리이며 `l = u tau`로 쓸 수 있다.

## 반도체와 절연체의 전도

- 반도체에서는 열진동이나 광흡수로 공유결합이 끊어지면 electron-hole pair가 생긴다.
- 전기장이 걸리면 conduction band 전자와 valence band 정공이 모두 전류에 기여한다.
- 이온 결정과 유리에서도 mobile ion이 있으면 전도도가 0이 아니며, 특히 Na+ 같은 이온 이동이 기여할 수 있다.
- 절연체도 완전한 0 전도도를 갖는 것은 아니지만, 전도도 범위가 금속과 반도체보다 훨씬 낮다.

## 박막 저항률과 Matthiessen 법칙

### Grain boundary scattering

- 다결정 박막에서는 grain boundary에서 전자가 산란되어 저항률이 증가한다.
- grain이 매우 작으면 평균 자유행로가 grain diameter에 의해 제한된다.
- 따라서 같은 재료라도 박막의 결정립 크기와 공정 이력에 따라 저항률이 달라진다.

### Matthiessen 법칙

- 서로 독립적인 산란 과정이 있을 때 전체 저항률은 각 산란 기여의 합으로 근사한다.

```text
rho = rho_T + rho_I
rho_T: thermal vibration scattering
rho_I: impurity scattering
```

- 실제로는 grain boundary, defect, dislocation에 의한 항도 추가적으로 고려할 수 있다.

## 금속 저항률의 온도 의존성

- 순수 금속은 충분히 높은 온도에서 저항률이 대체로 온도에 비례한다.

```text
rho proportional to T  at high enough T
```

- Cu는 100 K 이상에서 `rho proportional to T`, 100 K 이하에서 더 급한 온도 의존성을 보이며, 10 K 이하에서는 residual resistivity에 접근한다.
- residual resistivity는 불순물과 결함처럼 온도가 낮아도 남는 산란 때문이다.

### TCR

- thermal coefficient of resistivity는 온도 변화에 따른 저항률 변화의 기울기다.
- NiCr은 높은 저항률과 작은 TCR을 가져 heating wire에 적합하다.

## Nordheim 법칙과 합금 저항률

- 고용 합금에서 불순물 원자가 host lattice를 흐트러뜨려 impurity scattering을 만든다.

```text
rho_I = C X (1 - X)
```

- `X`는 solute concentration, `C`는 Nordheim coefficient다.
- dilute alloy에서는 `X << 1`이므로 `rho_I ≈ C X`로 볼 수 있다.
- solute와 solvent의 원자 크기나 퍼텐셜 차이가 클수록 `C`가 커진다.
- alloy scattering이 커지면 `rho_I`가 `rho_T`보다 우세해져 저항률의 온도 의존성이 약해진다.

### 열처리 효과

- 급랭된 amorphous 또는 변형된 금속은 결함과 무질서가 많아 저항률이 높다.
- annealing으로 결정성이 회복되면 산란이 줄고 저항률이 낮아질 수 있다.

## Eutectic phase diagram

- eutectic composition은 두 원소 합금에서 가장 낮은 녹는점을 만드는 조성이다.
- 조성이 solubility limit까지 증가하면 Nordheim 법칙에 따라 저항률이 증가한다.
- 특정 조성 이후에는 두 상이 공존하고, 저항률은 단순한 단상 고용체 법칙만으로 설명하기 어렵다.

## Hall effect

### 원리

- x 방향 전류와 z 방향 자기장 `B_z`가 있을 때 carrier는 Lorentz force를 받아 y 방향으로 분리된다.
- 이 전하 분리가 Hall electric field `E_H`를 만든다.

```text
F = q v x B
```

### Hall coefficient

```text
R_H = E_y / (J_x B_z)
For electrons: R_H = -1 / (e n)
For holes: R_H = +1 / (e p)
```

- `R_H < 0`이면 n-type, `R_H > 0`이면 p-type으로 판단한다.
- carrier concentration은 Hall coefficient의 크기에서 구할 수 있다.

### Hall mobility

```text
sigma = q mu n
mu = |R_H| sigma
```

- 강의 슬라이드에서는 n-type 전자 기준으로 `mu = -R_H sigma` 형태를 사용한다.

## 열전도

### 금속의 열전도

- 금속에서는 conduction electron이 뜨거운 영역에서 차가운 영역으로 에너지를 전달한다.
- 그래서 금속의 전기전도도가 높으면 대체로 열전도도도 높다.

### 비금속의 열전도

- 비금속은 자유전자가 거의 없으므로 lattice vibration, 즉 phonon이 열을 운반한다.
- 열전도 효율은 결합 강도, 원자 간 coupling, phonon propagation, 결함 산란에 좌우된다.

### Ohm 법칙과 Fourier 법칙

```text
I = -A sigma (dV/dx)
Q' = -A kappa (dT/dx)
```

- 전위구배는 전기전도의 driving force다.
- 온도구배는 열전도의 driving force다.

### Wiedemann-Franz-Lorenz 법칙

```text
kappa = C_WFL sigma T
```

- 순수 금속에서 `sigma proportional to 1/T`이면 `kappa`가 온도에 대해 비교적 일정해질 수 있다.

### 열저항

```text
Q' = Delta T / theta
```

- 열저항 `theta`는 전기저항처럼 온도차와 열유량의 비로 모델링할 수 있다.

## 시험 포인트

- drift velocity와 전류밀도 관계.
- Matthiessen 법칙의 물리적 의미.
- grain boundary가 박막 저항률을 키우는 이유.
- Nordheim 법칙 `rho_I = C X (1-X)` 해석.
- Hall coefficient 부호로 n-type과 p-type을 구분하는 방법.
- Ohm 법칙과 Fourier 법칙의 대응 관계.
- 금속과 비금속의 열전도 carrier 차이.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **06. Electrical and Thermal Conduction - 전기 열 전도**를 다루며, 원자 결합, 결정, 양자역학, band theory에서 반도체 소자 물성까지 이어지는 전자재료의 기초를 정리한다.
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
- **06. Electrical and Thermal Conduction - 전기 열 전도**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [05. Tunneling Blackbody Laser - 터널링 흑체 레이저](05-tunneling-blackbody-laser.md) · 다음: [07. Silicon Thin Film Crystallization - Si 박막 결정화](07-silicon-thin-film-crystallization-si.md)
