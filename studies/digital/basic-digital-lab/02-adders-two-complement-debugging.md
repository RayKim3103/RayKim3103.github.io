---
layout: page
title: "02. 가산기, 2의 보수, Verilog 디버깅"
permalink: /studies/digital/basic-digital-lab/02-adders-two-complement-debugging/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Basic_Digital_Experiment/lecture_notes/02%20Adders%20Two%20Complement%20Debugging%20-%20%EA%B0%80%EC%82%B0%EA%B8%B0%20%EB%B3%B4%EC%88%98%20%EB%94%94%EB%B2%84%EA%B9%85.md)

{% raw %}
tags: #basic-digital-experiment #verilog #adder #twos-complement #debugging

관련 노트: [Vivado Verilog 기본과 논리게이트](01-vivado-verilog-basics-and-logic-gates.md), [PYNQ GPIO, DEMUX, MUX, Decoder](03-pynq-gpio-demux-decoder.md)

## 핵심 요약

이 자료는 NAND 게이트의 보편성, half adder와 full adder, 2의 보수를 이용한 뺄셈, 4-bit adder-subtractor, 7-segment 출력, Verilog 디버깅을 다룬다. 단순 논리 게이트에서 산술 회로로 넘어가는 주차이며, 구조적 설계와 오류 분석이 중요하다.

## NAND 게이트

NAND는 AND의 출력을 반전한 게이트이다.

| A | B | A NAND B |
|---:|---:|---:|
| 0 | 0 | 1 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

NAND는 universal gate이므로 NOT, AND, OR 등을 모두 NAND 조합만으로 만들 수 있다. 디지털 회로에서 특정 게이트만으로 전체 회로를 구성할 수 있다는 점은 게이트 레벨 구현과 최적화에서 중요하다.

## Half Adder

Half adder는 두 1-bit 입력 `A`, `B`를 더해 `Sum`, `Carry`를 만든다.

| A | B | Sum | Carry |
|---:|---:|---:|---:|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 1 |

논리식은 다음과 같다.

```text
Sum   = A xor B
Carry = A and B
```

## Full Adder

Full adder는 아래 자리에서 올라온 carry-in까지 포함해 `A + B + Cin`을 계산한다.

```text
Sum  = A xor B xor Cin
Cout = (A and B) or (Cin and (A xor B))
```

여러 full adder를 직렬로 연결하면 ripple-carry adder를 만들 수 있다. 4-bit adder는 bit 0의 carry-out을 bit 1의 carry-in으로 넘기는 방식으로 구성된다.

## 2의 보수와 뺄셈

2의 보수는 음수를 표현하고 뺄셈을 덧셈으로 바꾸기 위해 사용한다.

```text
B의 2의 보수 = ~B + 1
A - B = A + (~B + 1)
```

1의 보수 표현은 `+0`과 `-0`이 동시에 존재하는 문제가 있지만, 2의 보수는 0 표현이 하나이며 덧셈 회로를 그대로 활용할 수 있다.

## 4-bit Adder-Subtractor

제어 입력 `M`으로 덧셈과 뺄셈을 선택한다.

| M | 동작 | B 입력 처리 | 초기 Carry-in |
|---:|---|---|---:|
| 0 | 덧셈 | B 그대로 | 0 |
| 1 | 뺄셈 | B 각 bit를 XOR로 반전 | 1 |

핵심 구조는 다음과 같다.

```text
B_i' = B_i xor M
Cin_0 = M
```

따라서 `M=1`이면 `A + ~B + 1`이 되어 뺄셈이 된다.

## 7-Segment 출력

7-segment는 숫자나 일부 문자를 7개의 LED segment 조합으로 표시한다. Verilog에서는 입력 숫자에 따라 segment 패턴을 반환하는 `function`이나 `case` 문으로 구현하기 쉽다.

주의할 점은 보드의 7-segment가 common anode인지 common cathode인지에 따라 1이 켜짐인지 꺼짐인지가 달라진다는 것이다. 실험에서는 constraint와 보드 회로에 맞춰 segment bit 패턴을 지정해야 한다.

## Verilog HDL 설계 관점

### 모델링 방식

| 방식 | 설명 |
|---|---|
| Structural modeling | 게이트나 하위 모듈 연결 중심 |
| Behavioral modeling | 동작을 알고리즘처럼 기술 |
| RTL modeling | 레지스터와 조합논리 사이 데이터 흐름 중심 |

### 포트와 데이터 타입

- `input`, `output`, `inout`으로 포트 방향을 정한다.
- 단순 연결 신호는 `wire`, 절차문에서 값을 저장ㆍ갱신하는 신호는 `reg`를 사용한다.
- 숫자 표기는 `[size]'[base][value]` 형식을 쓴다.

예:

```verilog
4'b1010
8'hFF
10'd125
```

## 디버깅 포인트

### Syntax error

문법 오류는 컴파일 단계에서 잡히는 경우가 많지만, 연결 실수는 waveform에서 `Z` 또는 `X`로 보일 수 있다.

- `Z`: high impedance, 구동하는 회로가 없거나 연결이 빠진 상태
- `X`: unknown, 충돌하거나 초기화되지 않은 상태

### Logical error

문법은 맞지만 논리식이 잘못된 경우이다. 예를 들어 full adder의 `Sum`에서 XOR가 빠지거나 carry 식이 틀리면 일부 입력 조합에서만 오류가 생긴다. 이런 오류는 모든 입력 조합을 testbench로 확인해야 찾기 쉽다.

### Index warning

버스 폭보다 큰 index를 접근하면 out-of-bound warning이 발생할 수 있다. 예를 들어 `[3:0]` 버스에서 bit 4를 읽으면 의도하지 않은 값이 나온다.

## 실험 결과 해석

논리 게이트, NAND 기반 회로, 2-bit adder, 4-bit adder-subtractor의 시뮬레이션과 FPGA 출력은 이론값과 일치한다. 특히 뺄셈 회로가 별도의 subtractor가 아니라 XOR 제어와 초기 carry-in만으로 구현된다는 점이 핵심이다.

## 시험ㆍ복습 체크포인트

- NAND만으로 NOT, AND, OR를 만들 수 있어야 한다.
- half adder와 full adder의 진리표와 논리식을 쓸 수 있어야 한다.
- 2의 보수 방식으로 `A - B`를 adder에서 구현하는 원리를 설명할 수 있어야 한다.
- waveform의 `X`, `Z`가 어떤 종류의 오류를 암시하는지 해석할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **02. 가산기, 2의 보수, Verilog 디버깅**를 다루며, Verilog와 FPGA 보드 실습을 통해 조합논리, 순차논리, SoC, 인터럽트, 디스플레이/오디오 IP를 구현한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 디지털 구현 주제에서는 cycle 단위 timing, reset 상태, handshake 조건을 파형으로 검증하는 습관이 중요하다.
- 합성 가능한 RTL과 testbench 전용 문법을 구분해 실제 hardware 의미를 확인한다.
- 실습의 핵심은 문법보다 hardware timing을 코드가 어떻게 표현하는지 이해하는 것이다.
- 조합논리와 순차논리는 always block sensitivity, assignment 방식, reset 설계에서 확실히 구분해야 한다.
- PYNQ/Zynq 실습에서는 PL 회로와 PS software가 주소맵, GPIO, interrupt로 연결된다.

### 문제 풀이 또는 구현 루틴

- RTL 작성 전 truth table 또는 state diagram을 만들고 testbench로 corner case를 먼저 고정한다.
- 보드에서 틀리면 simulation, constraints, clock/reset, IP address map, software driver 순서로 확인한다.
- 영상/오디오 실습은 sample clock, valid signal, buffer latency를 파형으로 추적한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- simulation 초기값에 기대면 FPGA 전원 인가 후 동작이 달라질 수 있다.
- latch inference는 대부분 빠진 default assignment에서 생긴다.
- MMIO 주소 offset과 bit mask를 잘못 쓰면 hardware가 맞아도 software에서 동작하지 않는다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 모듈은 combinational인가 sequential인가?
- reset 직후 모든 register 값이 정의되는가?
- 보드 출력이 틀릴 때 hardware와 software 중 어느 경계를 먼저 검증할 것인가?
- **02. 가산기, 2의 보수, Verilog 디버깅**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [01. Vivado Verilog 기본과 논리게이트](01-vivado-verilog-basics-and-logic-gates.md) · 다음: [03. PYNQ GPIO, DEMUX, MUX, Decoder](03-pynq-gpio-demux-decoder.md)
