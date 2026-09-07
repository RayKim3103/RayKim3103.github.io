---
layout: page
title: "07주차 예비 - PS 기반 LED 7-Segment AXI 제어"
permalink: /studies/systems/embedded-system/07-prep-ps-led-7-segment-axi/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/07%EC%A3%BC%EC%B0%A8%20%EC%98%88%EB%B9%84%20-%20PS%20%EA%B8%B0%EB%B0%98%20LED%207-Segment%20AXI%20%EC%A0%9C%EC%96%B4.md)

{% raw %}
이전: [06주차 결과 - ASCII 문자 생성기와 화면 표시](06-result-ascii.md)  
다음: [09주차 결과 - AXI Text-LCD PS PL 연동](09-result-axi-text-lcd-ps-pl.md)

## 핵심 요약

이번 예비보고서는 ARM Cortex-A9이 LED와 7-segment IP를 제어하는 시스템을 준비한다. 이전 주차가 PL 내부 RTL 중심이었다면, 이번 주차부터는 PS에서 AXI-Lite로 PL의 사용자 정의 IP register에 접근하여 하드웨어를 제어한다.

## 목표

- Zynq PS의 Cortex-A9, memory interface, I/O peripheral 역할을 이해한다.
- AXI-Lite가 제어 register 접근에 적합한 이유를 이해한다.
- LED/7-segment IP의 register map을 파악한다.
- PS와 사용자 정의 IP를 top RTL에서 연결하는 구조를 이해한다.

## Cortex-A9와 PS

Cortex-A9 dual core는 Zynq PS의 중심이다. 이번 실습에서는 다음 역할을 수행한다.

- software 실행
- AXI master로 PL IP register 접근
- DDR memory와 I/O peripheral 제어
- UART로 PC와 통신
- I2C로 RTC read 가능

## I/O Peripheral

Zynq PS는 GPIO, SPI, I2C, CAN, UART, SD, USB, Ethernet 등을 hard macro IP로 제공한다. 이번 실습 맥락에서는 UART와 I2C가 중요하다.

- UART: PC console과 통신
- I2C: RTC 같은 주변장치 read

## AMBA AXI-Lite

AXI-Lite는 AXI의 단순화된 버전으로, 대용량 burst transfer보다는 제어 register read/write에 적합하다. LED나 7-segment처럼 작은 제어값을 memory mapped register에 쓰는 경우에 잘 맞는다.

## LED/7-Segment IP Register

| Register | Address | 역할 |
|---|---|---|
| 7-Segment Control | `0x43C0_0000` | 8개 7-segment에 표시할 32비트 data |
| LED Control | `0x43C0_0004` | 하위 8비트로 8개 LED 제어 |

7-segment는 32비트를 4비트씩 나누어 8개 digit에 전달한다. LED는 LSB 8비트가 각각 LED on/off를 결정한다.

## 7-Segment 표시 구조

8개 display panel은 counter 기반으로 빠르게 순환 선택된다. `FND_COM7~FND_COM0`이 digit 선택을 담당하고, 각 digit에 대응하는 4비트 값이 segment pattern으로 변환된다.

## PS-PL 시스템 블록

이번 실습에서 사용하는 주요 블록:

- Cortex-A9
- 32bit GP AXI Master
- AXI Interconnect
- 사용자 정의 LED/7-segment IP
- Memory Controller
- UART
- I2C

PS의 `M_AXI_GP0`가 AXI master로 동작하고, AXI Interconnect를 통해 사용자 IP의 slave register에 접근한다.

## Top RTL 연결

직접 만든 LED/7-segment IP와 Xilinx 도구가 생성한 `system` 모듈을 함께 사용하려면 top RTL이 필요하다.

Top RTL의 역할:

- `system` 모듈 인스턴스화
- LED/7-segment IP 인스턴스화
- PS 쪽 DDR/FIXED_IO 포트 노출
- PL 쪽 LED/7-segment 출력 포트 노출
- AXI register 출력과 사용자 IP 입력 연결

## 정리

7주차 예비의 핵심은 PS가 단순히 보조 역할을 하는 것이 아니라, AXI-Lite를 통해 PL에 만든 IP를 직접 제어하는 master가 된다는 점이다. 이후 주차의 Text-LCD, TFT-LCD, interrupt, Linux device driver 실습은 모두 이 memory mapped I/O 개념 위에서 확장된다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **07주차 예비 - PS 기반 LED 7-Segment AXI 제어**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
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
- **07주차 예비 - PS 기반 LED 7-Segment AXI 제어**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [06주차 결과 - ASCII 문자 생성기와 화면 표시](06-result-ascii.md) · 다음: [09주차 결과 - AXI Text-LCD PS PL 연동](09-result-axi-text-lcd-ps-pl.md)
