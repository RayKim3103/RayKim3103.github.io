---
layout: page
title: "06. Channel Coding - 오류 제어 부호"
permalink: /studies/signals/digital-communications/06-channel-coding/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Communications/lecture_notes/06%20Channel%20Coding%20-%20%EC%98%A4%EB%A5%98%20%EC%A0%9C%EC%96%B4%20%EB%B6%80%ED%98%B8.md)

{% raw %}
## 핵심 요약

Channel coding은 bit error probability를 줄이거나 같은 BER에 필요한 `Eb/N0`를 낮추기 위해 redundancy를 추가하는 기법이다. 비용은 더 넓은 bandwidth와 낮아진 code rate이다. 이 강의는 repetition code, ARQ/FEC, parity check code, rectangular code, linear block code, syndrome decoding, Hamming code, cyclic code, coding gain을 다룬다.

## Channel Coding의 목적

목표:

- bit error probability 감소
- 같은 BER에서 required `Eb/N0` 감소

대가:

- redundancy 증가
- code rate 감소
- bandwidth 증가

## Bit Error와 Burst Error

bit error는 수신 bit가 송신 bit와 다른 경우이다.

예:

```text
Tx: 11100100
Rx: 10100100 -> BER = 1/8
Rx: 11000000 -> BER = 2/8
```

여러 비트가 연속적으로 틀리면 burst error라고 한다.

error 원인:

- noise
- interference
- bad channel
- shadowing/fading

## Repetition Code

가장 단순한 error-correcting code는 같은 bit를 여러 번 반복하는 것이다.

예: repetition factor `k = 3`

```text
m = 101001
c = 111 000 111 000 000 111
```

decoding은 majority vote로 수행할 수 있다.

장점:

- 구현이 매우 단순하다.
- 일부 1-bit error를 correction할 수 있다.

단점:

- code rate가 낮다.
- burst error에 약할 수 있다.
- bandwidth 낭비가 크다.

## Error Control Types

### ARQ

ARQ는 error를 detect한 뒤 재전송을 요청한다.

대표 방식:

- Stop-and-wait ARQ
- Continuous ARQ with pullback
- Continuous ARQ with selective repeat

특징:

- code가 비교적 간단하다.
- error가 있을 때만 retransmission하므로 adaptive하다.
- delay가 허용되는 시스템에 적합하다.

### FEC

FEC는 수신단에서 error correction까지 수행한다.

필요한 경우:

- delay가 큰 시스템
- retransmission이 어려운 시스템
- error가 너무 잦아 재전송 비용이 큰 시스템

## Code Rate and Redundancy

`(n, k)` block code:

- `k`: information bits
- `n`: codeword length
- `n-k`: redundancy 또는 parity bits

code rate:

```text
R_c = k / n
```

redundancy가 늘면 error control 능력은 좋아질 수 있지만 bandwidth 비용이 커진다.

## Parity Check Codes

### Single Parity Check

정보 bit들의 mod-2 sum을 parity bit로 추가한다.

- even parity: 전체 1의 개수가 짝수
- odd parity: 전체 1의 개수가 홀수

특징:

- error detection 가능
- 일반적으로 correction은 불가
- 짝수 개 error는 detect하지 못할 수 있다.

### Rectangular Code

product code 형태로 row/column parity를 붙인다.

특징:

- single error correction 가능
- block 구조를 이용해 오류 위치를 찾을 수 있다.

## Linear Block Code

linear code는 두 codeword를 mod-2로 더해도 또 다른 codeword가 되는 code이다.

systematic codeword:

```text
c = [parity bits | message bits]
```

generator matrix:

```text
G = [P : I_k]
c = m G
```

parity check matrix:

```text
H = [I_{n-k} : P^T]
```

오류가 없으면:

```text
c H^T = 0
```

## Minimum Distance

Hamming distance는 두 codeword 사이에서 다른 bit의 개수이다.

linear block code에서는 nonzero codeword의 최소 Hamming weight가 minimum distance가 된다.

minimum distance `d_min`의 의미:

- detection 능력: 최대 `d_min - 1`개 error detect 가능
- correction 능력: 최대 `floor((d_min - 1)/2)`개 error correction 가능

## Syndrome Decoding

수신 vector:

```text
r = c + e
```

syndrome:

```text
s = r H^T
```

오류가 없으면 `s = 0`이다. 오류가 있으면 syndrome은 error pattern에만 의존한다.

decoding 절차:

1. `s = r H^T` 계산
2. syndrome에 해당하는 coset leader 선택
3. error pattern `e` 추정
4. `c_hat = r + e`로 correction

## (7,4) Hamming Code

Hamming code는 `k = 4` message bits, `n = 7` codeword bits를 사용한다.

특징:

- 3 parity bits
- code rate `4/7`
- 1-bit error correction 가능

syndrome table을 이용하면 syndrome별 error pattern을 바로 찾을 수 있다.

### Worked example — 실제 G, H로 인코딩·오류정정

$$
P=\begin{bmatrix}1&1&0\\0&1&1\\1&1&1\\1&0&1\end{bmatrix},\qquad
G=[P\mid I_4]=\begin{bmatrix}1&1&0&1&0&0&0\\0&1&1&0&1&0&0\\1&1&1&0&0&1&0\\1&0&1&0&0&0&1\end{bmatrix},\qquad
H=[I_3\mid P^T]=\begin{bmatrix}1&0&0&1&0&1&1\\0&1&0&1&1&1&0\\0&0&1&0&1&1&1\end{bmatrix}
$$

메시지 $$m=(1,0,1,1)$$을 인코딩: $$c=mG=(1,0,0,1,0,1,1)$$(패리티 $$p_1p_2p_3=100$$ + 메시지 $$1011$$). 검산: $$Hc^T=(0,0,0)$$ — 유효한 codeword.

이제 5번째 비트($$m_2$$)에 오류가 생겨 $$r=(1,0,0,1,\mathbf{1},1,1)$$을 수신했다고 하자.

$$
s=Hr^T=(0,1,1)
$$

$$H$$의 다섯 번째 열이 정확히 $$(0,1,1)^T$$이므로 오류 위치는 5번째 비트. 그 비트를 뒤집으면 $$\hat c=(1,0,0,1,0,1,1)=c$$로 완전히 복원된다 — [프로젝트 노트](00-final-project-rayleigh-fading-voice-system.md)의 Hamming decoding이 바로 이 절차다.

한 비트 오류는 이렇게 항상 correction되지만, 2비트 이상 오류는 다른 유효 codeword로 잘못 "정정"될 수 있다($$d_{min}=3$$이라 1개 오류까지만 보장).

## Cyclic Code

Cyclic code는 linear block code의 부분집합이다.

두 성질:

- linearity: codeword들의 mod-2 sum도 codeword
- cyclic property: codeword를 cyclic shift해도 codeword

code polynomial:

```text
C(X) = C_0 + C_1 X + ... + C_{n-1} X^{n-1}
```

generator polynomial `g(X)`가 `(n, k)` cyclic code를 정의한다.

조건:

- `g(X)`의 degree는 `n-k`
- `g(X)`는 `X^n + 1`의 factor
- codeword는 `g(X)`의 multiple

## (7,4) Cyclic Code

`X^7 + 1`을 irreducible polynomial들로 factorization하여 `(7,4)` cyclic code를 만들 수 있다.

generator polynomial 예:

```text
g(X) = 1 + X + X^3
```

cyclic code는 shift-register encoder/decoder로 구현하기 좋다.

## Coding Gain

coding gain은 같은 BER을 달성하는 데 필요한 `Eb/N0`가 coding으로 얼마나 줄어드는지를 의미한다.

```text
coding gain = (Eb/N0)_uncoded - (Eb/N0)_coded
```

trade-off:

- error performance vs bandwidth
- power vs bandwidth
- data rate vs bandwidth

예를 들어 coded system은 더 많은 bandwidth를 쓰는 대신 required power를 낮출 수 있다.

## 시험 포인트

- ARQ와 FEC의 차이를 설명할 수 있어야 한다.
- `(n,k)` code에서 code rate와 redundancy를 계산한다.
- syndrome이 error pattern에만 의존한다는 점을 이해한다.
- Hamming distance와 correction/detection 능력의 관계를 기억한다.
- $$G$$, $$H$$ worked example을 직접 재현해 $$m=(1,0,1,1)$$을 인코딩하고, 5번째 비트 오류를 syndrome으로 찾아 정정할 수 있는가?
- cyclic code에서 generator polynomial의 역할을 안다.
- coding gain은 BER 기준 `Eb/N0` 절감량이다.

## 같이 보면 좋은 노트

- [Channel Coding Supplement - Entropy와 Shannon Limit](07-channel-coding-supplement-entropy-shannon-limit.md)
- [Final Project - Rayleigh Fading 음성 통신 시스템](00-final-project-rayleigh-fading-voice-system.md)
- [Channel Model - Multipath Fading과 Equalization](09-channel-model-multipath-fading-equalization.md)

{% endraw %}

---

이전: [05. Error Performance - Bandpass BER 성능](05-error-performance-bandpass-ber.md) · 다음: [07. Channel Coding Supplement - Entropy와 Shannon Limit](07-channel-coding-supplement-entropy-shannon-limit.md)
