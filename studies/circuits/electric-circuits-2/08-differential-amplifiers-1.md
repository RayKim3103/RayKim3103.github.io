---
layout: page
title: "08. Differential Amplifiers 1 - 차동 증폭기 기본"
permalink: /studies/circuits/electric-circuits-2/08-differential-amplifiers-1/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/08%20Differential%20Amplifiers%201%20-%20%EC%B0%A8%EB%8F%99%20%EC%A6%9D%ED%8F%AD%EA%B8%B0%20%EA%B8%B0%EB%B3%B8.md)

{% raw %}
## 핵심 요약

MOS differential pair는 두 입력의 차이 `Vin1 - Vin2`에 반응하고, 두 입력에 공통으로 들어오는 noise는 억제한다. small-signal 해석은 differential mode와 common mode로 나누어 수행한다. differential mode에서는 대칭성 때문에 half-circuit을 사용하고, common mode에서는 tail current source의 finite resistance가 중요해진다.

## Differential Pair 구조

입력:

```text
VG1, VG2
```

출력:

```text
Vout = VD1 - VD2
```

대칭 조건에서:

```text
Vin1 = Vin2 -> Vout1 = Vout2 -> Vout = 0
```

만약 `Vin1 > Vin2`이면 M1 current가 증가하고 `Vout1`은 감소한다.

## 왜 Differential Pair를 쓰는가

장점:

- 두 입력에 동시에 들어오는 noise를 cancel
- op-amp 입력단에 적합
- analog IC에서 matching과 symmetry를 활용하기 좋음
- SNR 향상

## Large-Signal 관점

tail current `ISS`는 두 transistor 사이에 나뉜다.

- `Vin1 = Vin2`: `ID1 = ID2 = ISS/2`
- `Vin1`이 커짐: `ID1` 증가, `ID2` 감소
- 충분히 큰 differential input에서는 한쪽 transistor가 tail current 대부분을 가져간다.

linear region은 `Vin1 ≈ Vin2` 근처의 작은 differential input 영역이다.

## Differential/Common Mode 분해

두 입력은 평균과 차이로 표현한다.

```text
VCM = (Vin1 + Vin2) / 2
Vid = Vin1 - Vin2

Vin1 = VCM + Vid/2
Vin2 = VCM - Vid/2
```

linear system처럼 DM 응답과 CM 응답을 superposition한다.

## Differential Mode 해석

DM에서는 회로가 anti-symmetric이다. 가운데 tail node는 AC ground로 볼 수 있다.

half-circuit:

```text
각 절반은 CS amplifier
```

`ro` 무시:

```text
Vout / Vid = - gm RD
```

`ro` 포함:

```text
Adm = - gm (RD || ro)
```

## Common Mode 해석

CM 입력에서는 두 입력이 같이 움직인다.

ideal tail current source이면:

```text
Vout1 = Vout2
Vout = Vout1 - Vout2 = 0
```

즉 differential output에서는 common-mode gain이 0이다.

finite tail resistance `RSS`가 있으면 각 single-ended output은 변할 수 있다. half-circuit에서는 source degeneration `2RSS`가 있는 CS처럼 해석할 수 있다.

## Input Common-Mode Range

입력 common-mode voltage가 너무 크거나 작으면 transistor saturation이 깨진다.

상한은 input transistor의 drain-source saturation 조건에서 결정된다.

```text
VCM,max ≈ VDD - ID RD + VTH
```

하한은 tail current source가 정상 동작할 수 있는 voltage headroom으로 결정된다.

설계 시에는:

- input pair saturation
- tail current source saturation
- output swing

을 모두 확인해야 한다.

## 시험 포인트

- `Vin1`, `Vin2`를 `VCM`, `Vid`로 분해한다.
- DM half-circuit에서 tail node가 AC ground가 되는 이유를 설명한다.
- DM gain은 CS gain과 같아진다.
- ideal tail current source에서 differential output CM gain은 0이다.
- ICMR는 모든 MOSFET saturation 조건으로 결정된다.

## 같이 보면 좋은 노트

- [Differential Amplifiers 2 - CMRR와 Active Load](09-differential-amplifiers-2-cmrr-active-load.md)
- [OTA and Op-Amp - OTA와 연산증폭기](15-ota-and-op-amp.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **08. Differential Amplifiers 1 - 차동 증폭기 기본**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 회로 주제에서는 DC 동작점, small-signal 모델, 주파수 응답, feedback 효과를 순서대로 분리한다.
- gain 식은 부호, loading, output resistance, capacitance가 들어가는 위치를 회로 노드에서 추적한다.
- midband gain만으로 평가하지 말고 input/output resistance, pole-zero, swing, noise, stability를 함께 봐야 한다.
- feedback은 gain을 희생해 bandwidth, 선형성, 저항 특성, 공정 민감도를 조절하는 도구다.
- filter와 oscillator는 s-domain pole 위치가 시간응답, 주파수응답, 안정성을 결정한다.

### 문제 풀이 또는 구현 루틴

- DC operating point, small-signal model, midband gain, pole-zero, feedback loop 순서로 해석한다.
- spec을 gain, bandwidth, noise, power, area, swing 제약으로 나누어 trade-off를 적는다.
- LTspice 결과는 operating point, AC response, transient response를 같은 회로에서 순차 확인한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- Miller effect를 놓치면 high-frequency pole을 크게 잘못 예측한다.
- loop gain과 closed-loop gain을 섞으면 feedback 회로 해석이 흐려진다.
- oscillator는 loop gain뿐 아니라 phase 조건과 amplitude stabilization이 필요하다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 회로의 dominant pole은 어느 node capacitance와 resistance가 만드는가?
- feedback이 입력/출력 저항을 어느 방향으로 바꾸는가?
- spec 하나를 개선할 때 power, noise, swing, stability 중 무엇이 나빠지는가?
- **08. Differential Amplifiers 1 - 차동 증폭기 기본**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [07. Bias Circuits and Current Mirrors - 바이어스와 전류미러](07-bias-circuits-and-current-mirrors.md) · 다음: [09. Differential Amplifiers 2 - CMRR와 Active Load](09-differential-amplifiers-2-cmrr-active-load.md)
