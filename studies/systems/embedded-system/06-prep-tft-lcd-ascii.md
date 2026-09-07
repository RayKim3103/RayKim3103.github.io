---
layout: page
title: "06주차 예비 - TFT-LCD ASCII 문자 출력"
permalink: /studies/systems/embedded-system/06-prep-tft-lcd-ascii/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/06%EC%A3%BC%EC%B0%A8%20%EC%98%88%EB%B9%84%20-%20TFT-LCD%20ASCII%20%EB%AC%B8%EC%9E%90%20%EC%B6%9C%EB%A0%A5.md)

{% raw %}
이전: [05주차 결과 - TFT-LCD 타이밍 BRAM 색상 패턴](05-result-tft-lcd-bram.md)  
다음: [06주차 결과 - ASCII 문자 생성기와 화면 표시](06-result-ascii.md)

## 핵심 요약

이번 예비보고서는 ASCII 문자 데이터를 TFT-LCD 화면에 출력하기 위한 구조를 정리한다. 핵심은 문자열 자체를 곧바로 화면에 보내는 것이 아니라, ASCII 코드, 문자 ROM, 문자 위치 좌표, 8x8 bitmap pixel 변환 과정을 거쳐 RGB video data로 출력한다는 점이다.

## 목표

- ASCII 코드의 의미와 구조를 이해한다.
- TFT-LCD 문자 출력용 주요 Verilog 모듈의 역할을 파악한다.
- Dual Port BRAM이 문자 데이터 저장에 사용되는 이유를 이해한다.
- JTAG 기반 보드 검증 조건을 확인한다.

## ASCII

ASCII는 7비트 문자 인코딩 표준이며 총 128개 코드를 정의한다.

| 구분 | 내용 |
|---|---|
| 제어 문자 | 통신/제어용, 출력되지 않는 문자 |
| 출력 문자 | 알파벳, 숫자, 특수문자, 공백 |
| 예시 | `A=65`, `B=66`, 공백 `0x20` |

실습에서는 각 문자를 8비트 값으로 저장하여 BRAM과 ROM에서 처리한다.

## 주요 모듈

| 모듈 | 역할 |
|---|---|
| `SVGA_DEFINES.v` | 해상도, porch, sync timing 등 상수 정의 |
| `SVGA_TIMING_GENERATION.v` | `HSYNC`, `VSYNC`, pixel/character 좌표 생성 |
| `CHAR_DISPLAY.v` | 화면에 표시할 문자열과 색상 결정 |
| `CHAR_GEN.v` | ASCII code를 문자 bitmap pixel로 변환 |
| `CHAR_GEN_ROM.v` | 각 ASCII 문자의 8x8 bitmap data 저장 |
| `CHAR_DPRAM.xci` | 출력할 ASCII 문자 code를 저장하는 dual-port BRAM |
| `VIDEO_OUT.v` | 최종 RGB와 sync 신호 출력 |

## BRAM 구조

실습에서 사용하는 `CHAR_DPRAM.xci`는 True Dual Port RAM이다.

- Port A: 문자 데이터를 write하는 용도
- Port B: 현재 화면 좌표에 대응하는 ASCII를 read하는 용도
- Width: 8bit
- Depth: 16383
- 초기값: remaining memory location을 `0x20`으로 설정하여 공백 문자로 초기화

Dual port 구조를 사용하면 한쪽 포트에서 화면에 표시할 문자를 계속 쓰는 동안, 다른 포트에서 문자 데이터를 읽어 bitmap 변환을 수행할 수 있다.

## 화면 문자 좌표

TFT-LCD는 480x272 해상도이고, 한 문자가 8x8 pixel block을 사용한다. 이론적으로는 가로 60문자, 세로 34문자 정도가 가능하지만, 실제 skeleton code에서는 안정적인 표시 범위를 위해 더 작은 문자 배열을 사용한다.

## 검증 조건

5주차와 같이 Cascaded JTAG 통신을 사용한다. BOOT_MODE J19가 2-3번 핀으로 연결되어 있는지 확인하고, 보드 실습을 진행한다.

## 정리

6주차 예비의 핵심은 문자 출력이 `문자열 -> ASCII -> 문자 ROM address -> 8x8 bitmap -> pixel_on -> RGB`의 흐름이라는 점이다. 특히 timing generator가 pixel 좌표뿐 아니라 현재 문자 line/column까지 제공한다는 점이 중요하다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **06주차 예비 - TFT-LCD ASCII 문자 출력**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
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
- **06주차 예비 - TFT-LCD ASCII 문자 출력**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [05주차 결과 - TFT-LCD 타이밍 BRAM 색상 패턴](05-result-tft-lcd-bram.md) · 다음: [06주차 결과 - ASCII 문자 생성기와 화면 표시](06-result-ascii.md)
