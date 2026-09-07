---
layout: page
title: "11주차 예비 - Push Button Interrupt System"
permalink: /studies/systems/embedded-system/11-prep-push-button-interrupt-system/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/11%EC%A3%BC%EC%B0%A8%20%EC%98%88%EB%B9%84%20-%20Push%20Button%20Interrupt%20System.md)

{% raw %}
이전: [10주차 결과 - AHB TFT-LCD 이미지 출력](10-result-ahb-tft-lcd.md)  
다음: [11주차 결과 - PL Interrupt와 PS Handler](11-result-pl-interrupt-ps-handler.md)

## 핵심 요약

이번 예비보고서는 Push Button으로 interrupt를 발생시키고, PS의 interrupt handler가 이를 처리한 뒤 PL의 LED를 제어하는 시스템을 준비한다. 핵심은 버튼 event가 polling이 아니라 interrupt request로 PS에 전달되고, GIC와 handler를 거쳐 다시 PL 제어 데이터로 돌아온다는 점이다.

## Interrupt

Interrupt는 실행 중인 program을 잠시 중단하고, 외부 event나 error, system call 등을 처리하도록 processor에 알리는 mechanism이다.

일반적인 처리 흐름:

1. main program 실행
2. interrupt 발생
3. return address 저장
4. interrupt vector로 jump
5. interrupt handler 실행
6. interrupt 처리 완료
7. return address load
8. 원래 실행 위치로 jump
9. main program 재개

## Zynq PS와 GIC

Zynq PS는 interrupt 처리를 위해 GIC(Generic Interrupt Controller)를 사용한다. PL에서 발생한 interrupt는 Fabric Interrupts를 통해 PS로 들어가며, 이번 실습에서는 `Core0_nIRQ` port를 활성화하여 CPU0이 interrupt를 처리하도록 한다.

## 이번 실습의 I/O

- Push Button: PL에서 입력 감지
- LED: PL에서 출력 제어
- UART: interrupt 발생 상태를 PC console에 출력
- AXI/AHB bridge: PS와 PL 사이 register read/write

## Interrupt 처리 과정

```text
Push Button 입력
-> PL Interrupt Generator IP
-> Core0_nIRQ
-> Zynq PS GIC
-> Interrupt Handler
-> AXI/AHB register write
-> PL LED control
```

## Interrupt Handler

Handler는 interrupt 원인을 확인하고, 그에 맞는 동작을 수행한 뒤, interrupt가 처리되었음을 알리는 함수다. 이번 실습에서는 `DeviceDriverHandler(void *CallbackRef)`가 사용된다.

Handler의 역할:

- 어떤 button interrupt인지 확인
- 해당 button에 mapping된 LED value 생성
- PL register에 LED value write
- interrupt 상태를 clear 또는 복원
- 처리 완료 flag 설정

## Interrupt Generator IP

PL 영역에 설계된 사용자 IP는 다음 역할을 담당한다.

- Push Button 입력 감지
- interrupt 상태 register 저장
- interrupt signal 출력
- PS가 register를 read하면 현재 interrupt 상태 제공
- PS가 LED register를 write하면 LED 출력 갱신

## 정리

11주차 예비의 핵심은 interrupt가 단순한 입력 신호가 아니라 **PL event -> PS exception 처리 -> handler -> PL 제어** 로 이어지는 시스템 동작이라는 점이다. 이 구조를 이해해야 결과 실습에서 AHB/APB bridge, GIC 설정, handler 코드의 역할이 분명해진다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **11주차 예비 - Push Button Interrupt System**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
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
- **11주차 예비 - Push Button Interrupt System**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [10주차 결과 - AHB TFT-LCD 이미지 출력](10-result-ahb-tft-lcd.md) · 다음: [11주차 결과 - PL Interrupt와 PS Handler](11-result-pl-interrupt-ps-handler.md)
