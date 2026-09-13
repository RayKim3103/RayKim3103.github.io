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

## Register 주소 간격 확인

`7-Segment Control`(`0x43C0_0000`)과 `LED Control`(`0x43C0_0004`)의 주소 차이는

$$
0x43C0\_0004 - 0x43C0\_0000 = 0x4 = 4\text{byte}
$$

AXI-Lite의 데이터 폭이 32비트(4byte)이므로, 두 register는 word 단위로 정확히 연속 배치되어 있다 — 이는 우연이 아니라 32비트 AXI-Lite에서 register map을 만들 때의 표준 관례(각 register가 4byte 경계에 정렬)다. 만약 세 번째 register가 추가된다면 `0x43C0_0008`에 위치할 것으로 예측할 수 있다.

## 복습 질문

- 두 register 주소가 4씩 차이나는 이유를 AXI-Lite의 데이터 폭과 연결해 설명할 수 있는가?
- AXI-Lite가 burst transfer를 지원하는 AXI-Full 대신 사용되는 이유를 LED/7-segment register 접근 특성과 연결해 설명할 수 있는가?
- `M_AXI_GP0`가 AXI master로 동작한다는 것이 무슨 의미인지, PS와 PL 중 누가 transaction을 시작하는지 설명할 수 있는가?

## 정리

7주차 예비의 핵심은 PS가 단순히 보조 역할을 하는 것이 아니라, AXI-Lite를 통해 PL에 만든 IP를 직접 제어하는 master가 된다는 점이다. 이후 주차의 Text-LCD, TFT-LCD, interrupt, Linux device driver 실습은 모두 이 memory mapped I/O 개념 위에서 확장된다.

{% endraw %}

---

이전: [06주차 결과 - ASCII 문자 생성기와 화면 표시](06-result-ascii.md) · 다음: [09주차 결과 - AXI Text-LCD PS PL 연동](09-result-axi-text-lcd-ps-pl.md)
