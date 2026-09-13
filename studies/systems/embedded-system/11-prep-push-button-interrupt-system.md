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

## Polling과 Interrupt 방식 비교

같은 "버튼 입력 감지" 문제를 두 방식으로 구현한다고 하면 차이가 분명해진다.

| 방식 | 동작 | 문제점/장점 |
|---|---|---|
| Polling | PS가 loop를 돌며 계속 button register를 read | CPU가 다른 일을 못 하고 계속 확인만 함(자원 낭비), 확인 주기보다 짧게 눌리면 놓칠 수 있음 |
| Interrupt | 버튼이 눌릴 때만 PL이 PS에 신호를 보내 실행을 중단시킴 | CPU는 평소 다른 작업을 하다가 필요한 순간에만 반응, event를 놓치지 않음 |

10주차까지의 실습(AXI/AHB register write)은 모두 PS가 능동적으로 접근을 시작하는 구조였다면, interrupt는 반대로 PL이 능동적으로 PS의 실행을 중단시키는 첫 사례다 — 이 방향 전환이 이번 주차의 핵심 개념이다.

## 복습 질문

- Polling 방식이 CPU 자원을 낭비하는 이유와, interrupt 방식이 이를 어떻게 해결하는지 설명할 수 있는가?
- "PL event → PS exception 처리 → handler → PL 제어"의 9단계 흐름에서, `Core0_nIRQ`가 어느 단계에 해당하는지 설명할 수 있는가?
- 이전 주차들(PS가 AXI로 PL을 write)과 이번 주차(PL이 PS를 interrupt)의 데이터/제어 흐름 방향이 어떻게 다른지 설명할 수 있는가?

## 정리

11주차 예비의 핵심은 interrupt가 단순한 입력 신호가 아니라 **PL event -> PS exception 처리 -> handler -> PL 제어** 로 이어지는 시스템 동작이라는 점이다. 이 구조를 이해해야 결과 실습에서 AHB/APB bridge, GIC 설정, handler 코드의 역할이 분명해진다.

{% endraw %}

---

이전: [10주차 결과 - AHB TFT-LCD 이미지 출력](10-result-ahb-tft-lcd.md) · 다음: [11주차 결과 - PL Interrupt와 PS Handler](11-result-pl-interrupt-ps-handler.md)
