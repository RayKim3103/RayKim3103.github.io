---
layout: page
title: "06. Cascode Amplifier - 캐스코드 증폭기"
permalink: /studies/circuits/electric-circuits-2/06-cascode-amplifier/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/06%20Cascode%20Amplifier%20-%20%EC%BA%90%EC%8A%A4%EC%BD%94%EB%93%9C%20%EC%A6%9D%ED%8F%AD%EA%B8%B0.md)

{% raw %}
## 핵심 요약

Cascode amplifier는 CS stage 뒤에 CG stage를 붙인 구조이다. CS가 transconductance를 만들고, CG가 current buffer처럼 동작하여 output resistance를 크게 키운다. 결과적으로 높은 voltage gain을 얻을 수 있지만, transistor를 stack하기 때문에 headroom 문제가 생긴다.

## CG with Source Resistance

CG stage의 source에 저항 `RS`가 보이면 output resistance가 커진다.

```text
Rout = RD || [ro + RS(1 + gm ro)]
```

핵심은 source resistance가 `1 + gm ro`만큼 증폭되어 drain에서 보인다는 것이다.

## Cascode 구조

Cascode:

```text
CS + CG
```

하단 CS:

- input voltage를 drain current로 변환
- `Gm ≈ gm1`

상단 CG:

- current를 output node로 전달
- output resistance를 boost

## Output Resistance

두 MOS의 `ro1`, `ro2`를 고려하면:

```text
Rout ≈ RD || [ro2 + ro1(1 + gm2 ro2)]
```

`gm2 ro2 >> 1`이면:

```text
Rout ≈ RD || (gm2 ro2 ro1)
```

따라서 단순 CS보다 훨씬 큰 output resistance를 얻는다.

## Voltage Gain

CS의 transconductance와 cascode output resistance로 gain이 결정된다.

```text
Av ≈ - gm1 Rout
```

active load까지 cascode로 만들면:

```text
Rout,N ≈ gm2 ro2 ro1
Rout,P ≈ gm3 ro3 ro4
Av ≈ -gm1 (Rout,N || Rout,P)
```

## Cascode의 장점

- 높은 output resistance
- 높은 voltage gain
- Miller effect 감소
- bandwidth 개선 가능

## Cascode의 단점

가장 큰 단점은 headroom이다.

- 여러 MOSFET이 supply 사이에 stack된다.
- 각 transistor가 saturation을 유지할 최소 `VDS` 또는 `VSD`가 필요하다.
- low-voltage process에서 voltage swing이 제한된다.

## 시험 포인트

- cascode는 `CS + CG`로 이해한다.
- `RS`가 CG output에서 `RS(1+gmro)`로 boost됨을 기억한다.
- gain은 `-gm1 Rout`로 보는 것이 핵심이다.
- cascode의 장점은 큰 gain/bandwidth, 단점은 headroom이다.

## 같이 보면 좋은 노트

- [Source Follower and Common-Gate - SF CG 증폭기](05-source-follower-and-common-gate-sf-cg.md)
- [Frequency Response of Cascode SF Differential - 고주파 응답 비교](14-frequency-response-of-cascode-sf-differential.md)
- [Bias Circuits and Current Mirrors - 바이어스와 전류미러](07-bias-circuits-and-current-mirrors.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **06. Cascode Amplifier - 캐스코드 증폭기**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **06. Cascode Amplifier - 캐스코드 증폭기**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [05. Source Follower and Common-Gate - SF CG 증폭기](05-source-follower-and-common-gate-sf-cg.md) · 다음: [07. Bias Circuits and Current Mirrors - 바이어스와 전류미러](07-bias-circuits-and-current-mirrors.md)
