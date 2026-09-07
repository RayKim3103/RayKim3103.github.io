---
layout: page
title: "01. Vivado Verilog 기본과 논리게이트"
permalink: /studies/digital/basic-digital-lab/01-vivado-verilog-basics-and-logic-gates/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Basic_Digital_Experiment/lecture_notes/01%20Vivado%20Verilog%20Basics%20and%20Logic%20Gates%20-%20Vivado%20Verilog%20%EA%B8%B0%EB%B3%B8%EA%B3%BC%20%EB%85%BC%EB%A6%AC%EA%B2%8C%EC%9D%B4%ED%8A%B8.md)

{% raw %}
tags: #basic-digital-experiment #verilog #vivado #logic-gate #testbench

관련 노트: [가산기, 2의 보수, Verilog 디버깅](02-adders-two-complement-debugging.md)

## 핵심 요약

이 자료는 Vivado에서 Verilog 프로젝트를 만들고, 논리 게이트와 2-bit adder를 설계ㆍ시뮬레이션ㆍFPGA 보드에 연결하는 기본 흐름을 다룬다. 핵심은 하드웨어 모듈을 `design source`로 작성하고, 입력 변화를 만들어 주는 `simulation source`/testbench로 검증한 뒤, top module과 constraint 파일을 통해 실제 보드의 스위치ㆍ버튼ㆍLED 핀에 연결하는 것이다.

## Vivado 설계 흐름

1. 프로젝트 생성
   - 사용할 보드나 FPGA part를 선택한다.
   - 설계 코드는 `Design Sources`, 검증용 코드는 `Simulation Sources`에 둔다.

2. 모듈 작성
   - Verilog의 기본 단위는 `module`이다.
   - 입력은 `input`, 출력은 `output`으로 선언한다.
   - 조합논리는 `assign` 문으로 직접 표현할 수 있다.

3. 테스트벤치 작성
   - 테스트벤치는 실제 회로로 합성되는 코드가 아니라 시뮬레이션용 코드이다.
   - 입력 신호는 값을 바꿔야 하므로 보통 `reg`로 선언하고, 출력은 DUT가 구동하므로 `wire`로 둔다.
   - `initial begin ... end` 블록에서 시간 순서대로 입력을 바꾼다.
   - `#10`은 `timescale 1ns/1ps` 기준으로 10 ns 지연을 뜻한다.
   - 모든 입력 조합을 순서대로 넣고 waveform이 진리표와 일치하는지 확인한다.

4. Top module과 constraint
   - 하위 논리 모듈을 top module에서 인스턴스화한다.
   - top module의 포트를 보드의 스위치, 버튼, LED와 연결한다.
   - XDC constraint 파일에서 실제 핀 번호와 I/O standard를 지정한다.

## Verilog 기본 문법

### 모듈 인스턴스화

하위 모듈을 사용할 때는 다음 두 방식이 가능하다.

```verilog
// 순서 기반 연결
and_gate u0(a, b, y);

// 이름 기반 연결
and_gate u0(
    .a(a),
    .b(b),
    .y(y)
);
```

이름 기반 연결은 포트 수가 많아질수록 실수를 줄이기 쉽다.

### 논리 연산자

| 게이트 | Verilog 표현 | 의미 |
|---|---:|---|
| AND | `a & b` | 둘 다 1일 때 1 |
| OR | `a | b` | 하나라도 1이면 1 |
| NOT | `~a` | 반전 |
| NAND | `~(a & b)` | AND 후 반전 |
| NOR | `~(a | b)` | OR 후 반전 |
| XOR | `a ^ b` | 서로 다르면 1 |

## 논리 게이트 실험

### 실험 목적

기본 논리 게이트를 Verilog로 구현하고, 각 입력 조합에 대한 출력이 진리표와 일치하는지 확인한다.

### 구현 포인트

- 2입력 게이트는 입력 조합이 `00`, `01`, `10`, `11` 네 가지이다.
- 테스트벤치에서 각 조합을 10 ns 간격으로 넣어 waveform을 비교한다.
- FPGA 보드에서는 스위치를 입력으로, LED를 출력으로 연결해 실제 동작을 확인한다.

### 해석

시뮬레이션 waveform에서 각 게이트의 출력은 이론 진리표와 일치한다. 실제 보드에서도 스위치 조작에 따라 LED가 같은 방식으로 켜지고 꺼지므로, RTL 코드와 핀 연결이 정상임을 확인할 수 있다.

## 2-bit Adder 실험

### 실험 목적

2-bit 입력 두 개를 더해 합을 출력하는 회로를 설계한다. 기본 게이트 실험에서 배운 조합논리 표현이 더 복잡한 산술 회로로 확장되는 과정을 확인한다.

### 구현 포인트

- 2-bit 수의 합은 최대 `3 + 3 = 6`이므로 결과 표현에는 최소 3 bit가 필요하다.
- 입력 조합을 testbench에서 바꿔가며 예상 합과 waveform 출력을 비교한다.
- 보드에서는 입력 스위치와 출력 LED를 사용해 덧셈 결과를 직접 확인한다.

## 자주 틀리는 지점

- 테스트벤치의 입력을 `wire`로 선언하면 절차문 안에서 값을 대입할 수 없다.
- top module 포트 이름과 XDC constraint의 이름이 다르면 보드 입출력이 연결되지 않는다.
- `#10` 같은 delay는 합성용 회로 지연이 아니라 시뮬레이션 시간 제어이다.
- 논리 NOT `~`와 논리 부정 `!`는 용도가 다르다. 비트 단위 반전에는 `~`를 사용한다.

## 시험ㆍ복습 체크포인트

- `Design Source`와 `Simulation Source`의 차이를 설명할 수 있어야 한다.
- `reg`와 `wire`를 테스트벤치 관점에서 구분할 수 있어야 한다.
- AND, OR, NOT, NAND, NOR, XOR의 Verilog 표현과 진리표를 쓸 수 있어야 한다.
- top module, module instantiation, constraint 파일이 각각 어떤 역할을 하는지 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **01. Vivado Verilog 기본과 논리게이트**를 다루며, Verilog와 FPGA 보드 실습을 통해 조합논리, 순차논리, SoC, 인터럽트, 디스플레이/오디오 IP를 구현한다.
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
- **01. Vivado Verilog 기본과 논리게이트**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

다음: [02. 가산기, 2의 보수, Verilog 디버깅](02-adders-two-complement-debugging.md)
