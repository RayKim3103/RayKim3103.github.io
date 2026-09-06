---
layout: page
title: "14. Frequency Response of Cascode SF Differential - 고주파 응답 비교"
permalink: /studies/circuits/electric-circuits-2/14-frequency-response-of-cascode-sf-differential/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/14%20Frequency%20Response%20of%20Cascode%20SF%20Differential%20-%20%EA%B3%A0%EC%A3%BC%ED%8C%8C%20%EC%9D%91%EB%8B%B5%20%EB%B9%84%EA%B5%90.md)

## 핵심 요약

이 강의는 cascode, source follower, differential amplifier의 frequency response를 비교한다. cascode는 Miller effect를 줄여 CS보다 bandwidth가 좋아질 수 있고, source follower는 gain이 1에 가까워 Miller effect가 거의 없어 매우 빠르다. differential amplifier의 differential-mode 응답은 CS와 유사하고, common-mode 응답은 tail source capacitance와 finite resistance 때문에 주파수에 따라 증가할 수 있다.

## Cascode Frequency Response

cascode는 CS 뒤에 CG를 붙인 구조이다.

해석 절차:

1. MOS capacitance 추가
2. Miller approximation 적용
3. node별 capacitance로 단순화
4. pole frequency 추정

cascode의 `Cgd1`은 CS처럼 큰 Miller multiplication을 겪지 않고, 대략 `1 + gm1/gm2` 정도의 작은 factor로 보인다.

## Cascode 주요 Pole

대표 node:

- input node
- internal node Y
- output node

input pole은 대략 `Cgs1`, `Cgd1` 관련 capacitance와 source resistance에 의해 정해진다.

output pole:

```text
wp,out ≈ 1 / [RL (Cdb + Cgd)]
```

중간 node pole도 존재하지만, cascode는 중간 node resistance가 낮아 pole이 높은 주파수로 밀릴 수 있다.

## CS vs Cascode

자료의 Razavi 예시 비교:

- CS 3-dB bandwidth: 약 250 MHz
- Cascode 3-dB bandwidth: 약 440 MHz

해석:

- cascode는 gain도 크고 bandwidth도 개선될 수 있다.
- 단점은 headroom과 출력 swing 제한이다.

## Source Follower Frequency Response

source follower는 voltage gain이 1보다 약간 작다.

input capacitance:

```text
Cin ≈ Cgd + Cgs(1 - Av)
```

`Av ≈ 1`이므로 `Cgs(1-Av)`가 작아 input Miller effect가 거의 없다.

output capacitance:

```text
Cout ≈ Csb + CL
```

output pole:

```text
wp,out ≈ gm / (Csb + CL)
```

source follower는 보통 CS보다 bandwidth가 크고, load capacitance `CL`에 의해 제한될 수 있다.

## Differential Amplifier DM Response

differential mode에서는 half-circuit이 CS amplifier와 같다.

```text
Adm ≈ -gm RD
```

frequency response도 CS와 유사하게 `Cgd` Miller effect가 중요하다.

dominant pole 근사:

```text
wp ≈ 1 / [RS(Cgs + Cgd(1 + gmRD))]
```

## Differential Amplifier CM Response

common mode에서는 tail current source가 MOSFET으로 만들어지며 finite resistance와 capacitance를 가진다.

저주파에서는 tail source의 큰 resistance가 common-mode gain을 작게 만든다.

고주파에서는 capacitance 때문에 tail node가 충분히 고정되지 못하고, common-mode gain이 증가할 수 있다.

핵심:

- low frequency: CM rejection 좋음
- high frequency: CM gain 증가 가능
- 고주파 CMRR 저하

## 시험 포인트

- cascode는 Miller effect를 줄여 CS보다 bandwidth가 좋아질 수 있다.
- source follower는 `Av ≈ 1`이므로 Miller effect가 작다.
- SF bandwidth는 `CL`에 의해 제한되기 쉽다.
- differential mode response는 CS와 유사하다.
- common-mode response는 high frequency에서 악화될 수 있다.

## 같이 보면 좋은 노트

- [Cascode Amplifier - 캐스코드 증폭기](06-cascode-amplifier.md)
- [Frequency Response of CS - CS 주파수 응답](12-frequency-response-of-cs.md)
- [OTA and Op-Amp - OTA와 연산증폭기](15-ota-and-op-amp.md)



---

이전: [13. Frequency Response of CS Degeneration and CG - Degeneration CG 응답](13-frequency-response-of-cs-degeneration-and-cg.md) · 다음: [15. OTA and Op-Amp - OTA와 연산증폭기](15-ota-and-op-amp.md)
