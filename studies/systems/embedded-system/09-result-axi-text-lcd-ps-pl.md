---
layout: page
title: "09주차 결과 - AXI Text-LCD PS PL 연동"
permalink: /studies/systems/embedded-system/09-result-axi-text-lcd-ps-pl/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/09%EC%A3%BC%EC%B0%A8%20%EA%B2%B0%EA%B3%BC%20-%20AXI%20Text-LCD%20PS%20PL%20%EC%97%B0%EB%8F%99.md)

{% raw %}
이전: [07주차 예비 - PS 기반 LED 7-Segment AXI 제어](07-prep-ps-led-7-segment-axi.md)  
다음: [10주차 결과 - AHB TFT-LCD 이미지 출력](10-result-ahb-tft-lcd.md)

## 핵심 요약

이번 실습은 PS에서 문자열 데이터를 만들고, AXI interface를 통해 PL의 Text-LCD IP register에 전달하여 LCD를 제어하는 구조를 구현한다. 4주차 Text-LCD controller가 고정 문자열을 내부에서 만들었다면, 9주차는 `REG_A~REG_H`를 통해 PS가 출력 문자열을 공급한다.

## 시스템 구성

| 블록 | 역할 |
|---|---|
| `processing_system7_0` | Zynq PS, ARM Cortex-A9와 DDR/I/O 담당 |
| `ps7_0_axi_periph` | AXI Interconnect, master/slave 연결 |
| `rst_ps7_0_50M` | AXI peripheral과 interconnect reset 생성 |
| `textlcd_0` | AXI slave register를 가진 Text-LCD IP |
| `system_wrapper` | Block Design을 RTL 모듈로 감싼 wrapper |
| `top.v` | PS wrapper와 Text-LCD PL logic 연결 |

데이터 흐름은 다음과 같다.

```text
PS firmware
-> AXI GP master
-> AXI Interconnect
-> textlcd AXI slave register
-> REG_A~REG_H
-> PL textlcd controller
-> LCD RS/RW/E/Data
```

## `REG_A~REG_H`

`REG_A~REG_H`는 각각 32비트 register이다.

- 1문자 = 8비트
- 1 register = 4문자
- 8 register = 32문자
- Text-LCD 2행 x 16문자에 대응

`REG_A~REG_D`는 1행, `REG_E~REG_H`는 2행에 해당한다.

## `top.v` 연결

`top.v`는 `system_wrapper`와 `textlcd`를 동시에 인스턴스화한다.

- PS 쪽 DDR/FIXED_IO 포트는 `system_wrapper`에 연결
- `system_wrapper`의 `REG_A~REG_H` 출력은 내부 wire `reg_a~reg_h`로 받음
- 이 wire를 `textlcd` 모듈의 입력으로 전달
- Text-LCD 모듈은 4주차와 같은 timing sequence로 LCD 제어

## `textlcd.v` 변경점

4주차와 비교한 변경 사항:

1. `input wire [31:0] reg_a~reg_h` 포트를 추가했다.
2. 기존에 내부에서 고정 문자열을 만들던 `assign reg_a = ...` 부분을 주석 처리했다.
3. PS가 AXI register에 쓴 값이 그대로 LCD 출력 데이터로 사용된다.

counter x2000, counter x40, `lcd_mode`, `set_data`를 이용한 LCD timing 제어는 기존과 동일하다.

## `TextlcdApp.c` 기본 동작

기본 C application은 두 문자열 쌍을 번갈아 출력한다.

- 1번째 출력: `"RPS-Z7020-TK BD."`, `"Huins Co,. Ltd. "`
- 2번째 출력: `"Chapter9 Example"`, `"TextLCD Done...!"`

`TEXTLCD_mWriteReg(BASEADDR, offset, data)`를 통해 4문자씩 32비트로 묶어 register에 write한다.

문자 packing 예:

```c
up_line[i*4+3]
+ (up_line[i*4+2] << 8)
+ (up_line[i*4+1] << 16)
+ (up_line[i*4] << 24)
```

offset은 `i*4`로 증가하여 `REG_A~REG_D`에 접근하고, `i*4+16`은 `REG_E~REG_H`에 접근한다.

## Quiz: 우측 Shift와 Count

목표:

- 윗줄: `Count: xx`
- 아랫줄: 조원 이니셜
- 0.25초마다 오른쪽으로 한 칸 circular shift
- `Count: xx`가 우측으로 완전히 사라질 때마다 count 증가

구현:

- `right_shift_one(char *arr, int len)` 함수로 배열을 한 칸 오른쪽 rotate
- `iteration % 16`만큼 shift 적용
- `count = iteration / 16`
- delay loop를 `100000000`에서 `20000000`으로 줄여 약 0.25초 구현

## Delay와 UART 고찰

`for (i=0; i<100000000; i++) {}`의 실제 지연은 약 1.25초로 측정되었다. Cortex-A9 clock과 loop instruction cycle 수를 고려하면 합리적인 결과다. UART는 PS의 출력 문자열을 PC serial console로 전달하여 `printf` 결과를 확인하는 통로다.

## 정리

9주차 결과의 핵심은 PS가 문자열을 생성하고, AXI register에 32비트 단위로 write하면, PL의 Text-LCD controller가 이를 timing에 맞춰 LCD에 표시한다는 점이다. 즉, Text-LCD 출력 데이터의 출처가 PL 내부 고정값에서 PS software로 이동했다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **09주차 결과 - AXI Text-LCD PS PL 연동**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 디지털 구현 주제에서는 cycle 단위 timing, reset 상태, handshake 조건을 파형으로 검증하는 습관이 중요하다.
- 합성 가능한 RTL과 testbench 전용 문법을 구분해 실제 hardware 의미를 확인한다.
- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- embedded 개발은 hardware address map, bus protocol, interrupt, kernel/user boundary가 정확히 맞아야 한다.

### 문제 풀이 또는 구현 루틴

- 보드 문제는 bitstream, address map, device tree, driver probe, user app access 순서로 확인한다.
- 레지스터 제어는 base address, offset, bit field, read/write side effect를 표로 정리한다.
- interrupt는 hardware source, controller, device tree binding, ISR registration, user notification 경로를 추적한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- MMIO를 일반 메모리처럼 다루면 최적화와 ordering 문제가 생긴다.
- device tree compatible 문자열이 driver와 맞지 않으면 probe가 호출되지 않는다.
- interrupt clear 순서를 놓치면 ISR이 반복 호출되거나 edge를 잃을 수 있다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- peripheral의 register map과 실제 Vivado address가 일치하는가?
- kernel log에서 probe, interrupt, read/write 경로가 어디까지 도달하는가?
- user space 오류가 hardware, driver, permission 중 어디에서 시작되는가?
- **09주차 결과 - AXI Text-LCD PS PL 연동**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [07주차 예비 - PS 기반 LED 7-Segment AXI 제어](07-prep-ps-led-7-segment-axi.md) · 다음: [10주차 결과 - AHB TFT-LCD 이미지 출력](10-result-ahb-tft-lcd.md)
