---
layout: page
title: "11주차 결과 - PL Interrupt와 PS Handler"
permalink: /studies/systems/embedded-system/11-result-pl-interrupt-ps-handler/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/11%EC%A3%BC%EC%B0%A8%20%EA%B2%B0%EA%B3%BC%20-%20PL%20Interrupt%EC%99%80%20PS%20Handler.md)

{% raw %}
이전: [11주차 예비 - Push Button Interrupt System](11-prep-push-button-interrupt-system.md)  
다음: [12주차 결과 - Zynq Ubuntu Root File System 구성](12-result-zynq-ubuntu-root-file-system.md)

## 핵심 요약

이번 실습에서는 PL의 Push Button 입력으로 interrupt를 만들고, PS의 GIC와 handler가 이를 처리한 뒤 다시 PL LED를 제어했다. Verilog는 button, interrupt register, LED register를 담당하고, C code는 GIC 초기화와 interrupt routine을 담당한다.

## HW/SW 역할 분리

| 영역 | 역할 |
|---|---|
| PL Verilog | 버튼 edge 감지, interrupt status 저장, LED 출력, APB slave 동작 |
| PS C code | GIC 초기화, handler 등록, interrupt enable, status read, LED value write |
| Bridge | AXI -> AHB -> APB 변환으로 PS register 접근 연결 |

데이터 흐름:

```text
PB input
-> pb_intr
-> INTR
-> Core0_nIRQ / GIC
-> DeviceDriverHandler
-> Xil_In32LE / Xil_Out32LE
-> ahb2apb_zynq
-> pb_intr LED register
```

## `ahb2apb_zynq.v`

AHB master와 APB slave 사이의 bridge다. AHB의 valid transaction을 감지하면 APB의 `PSEL`, `PENABLE`, `PWRITE`, `PADDR`, `PWDATA`로 변환한다.

주요 동작:

- `st_ctrl=00`: idle 상태
- `M_AHB_htrans[1]=1`: valid transfer 감지
- 다음 상태에서 `PENABLE=1`로 APB access 수행
- `PADDR=reg_addr`, `PWDATA=M_AHB_hwdata`
- read data는 `PRDATA`를 `M_AHB_hrdata`로 전달

APB는 AHB보다 단순하므로, bridge는 복잡한 bus transaction을 peripheral register 접근 형태로 낮춰주는 역할을 한다.

## `pb_intr.v`

`pb_intr`는 interrupt generator이자 LED controller이다.

| register/signal | 역할 |
|---|---|
| `curr_intr[3:0]` | button별 interrupt 상태 |
| `curr_led[7:0]` | LED 출력 값 |
| `pb_1d`, `pb_2d` | button 입력 2단 delay |
| `INTR` | interrupt output |
| `PRDATA` | APB read data |
| `PWDATA` | APB write data |

버튼을 눌렀다가 떼는 순간 `pb_2d[i] && !pb_1d[i]` 조건으로 해당 interrupt bit가 0이 된다. PS가 처리 후 `PWDATA[i]=1`을 write하면 interrupt 상태가 다시 1로 복원된다. LED register address에 write하면 `curr_led`가 갱신된다.

## C 코드 구조

| 함수 | 역할 |
|---|---|
| `main` | interrupt generator 초기화 후 GIC example 실행 |
| `ScuGicExample` | GIC config lookup, 초기화, self-test, handler 등록, interrupt enable |
| `SetUpInterruptSystem` | ARM exception handler 등록 및 interrupt enable |
| `DeviceDriverHandler` | interrupt 원인 read, LED value write, interrupt 처리 완료 |

## GIC 초기화 흐름

1. `XScuGic_LookupConfig(DeviceId)`로 GIC hardware config 조회
2. `XScuGic_CfgInitialize`로 GIC instance 초기화
3. `XScuGic_SelfTest`로 정상 동작 확인
4. `Xil_ExceptionRegisterHandler`로 ARM exception과 GIC handler 연결
5. `Xil_ExceptionEnable`로 processor interrupt enable
6. `XScuGic_Connect`로 interrupt ID와 `DeviceDriverHandler` 연결
7. `XScuGic_Enable`로 해당 interrupt ID enable

## 기본 Handler 동작

`DeviceDriverHandler`는 `Xil_In32LE(AXI2AHBLite)`로 interrupt status를 읽는다. 예를 들어 button 0이 눌려 bit 0이 0이면 다음 동작을 한다.

- `Xil_Out32LE(AXI2AHBLite, pb | 1)`로 interrupt bit 복원
- `Xil_Out32(AXI2AHBLite + 4, (1 << 7) | (1 << 6))`로 LED 두 개 on
- `InterruptProcessed = TRUE`

button별로 서로 다른 LED 2개가 켜지도록 mapping되어 있다.

## Quiz: LED 2개 번갈아 점등

목표는 특정 button이 눌리면 해당하는 LED 2개가 1초 간격으로 번갈아 켜지고, 다른 button이 눌리면 즉시 해당 동작으로 전환되는 것이다.

구현 방식:

- handler 내부에서 `while(1)`로 현재 button 동작 유지
- loop마다 interrupt status를 다시 read
- 다른 button bit가 0이면 `break`
- 두 개의 delay loop를 사용해 좌/우 LED를 번갈아 write
- `InterruptProcessed = TRUE`를 제거해 handler 흐름을 계속 유지

## 고찰

- `Xil_ExceptionEnable`은 ARM processor가 interrupt를 받을 수 있게 하는 전체 enable이다.
- `XScuGic_Enable`은 GIC에서 특정 interrupt ID를 enable하는 함수다.
- `sleep(1)`은 구현은 간단하지만 sleep 중 다른 button interrupt에 즉시 반응하기 어려워 quiz 요구사항에는 부적합하다.
- `ReadRTC` 기반 구현은 timing은 좋지만 `xil_printf()` 유무에 따라 동작이 바뀌는 문제가 있었다. system call delay가 register update timing에 영향을 준 것으로 추정된다.

## 정리

11주차 결과의 핵심은 interrupt 처리 전체가 HW와 SW의 협업이라는 점이다. PL은 event를 만들고 status를 제공하며, PS는 GIC와 handler로 event를 해석하고 다시 PL register를 써서 LED 동작을 만든다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **11주차 결과 - PL Interrupt와 PS Handler**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- embedded 개발은 hardware address map, bus protocol, interrupt, kernel/user boundary가 정확히 맞아야 한다.
- device driver는 kernel object lifecycle, file operation, memory-mapped I/O, synchronization을 다룬다.
- bootloader, kernel, device tree, root filesystem은 각 단계가 다음 단계에 hardware 정보를 넘기는 chain이다.

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
- **11주차 결과 - PL Interrupt와 PS Handler**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [11주차 예비 - Push Button Interrupt System](11-prep-push-button-interrupt-system.md) · 다음: [12주차 결과 - Zynq Ubuntu Root File System 구성](12-result-zynq-ubuntu-root-file-system.md)
