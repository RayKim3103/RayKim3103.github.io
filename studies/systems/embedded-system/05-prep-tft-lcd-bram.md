---
layout: page
title: "05주차 예비 - TFT-LCD 구조와 BRAM 영상 표시"
permalink: /studies/systems/embedded-system/05-prep-tft-lcd-bram/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/05%EC%A3%BC%EC%B0%A8%20%EC%98%88%EB%B9%84%20-%20TFT-LCD%20%EA%B5%AC%EC%A1%B0%EC%99%80%20BRAM%20%EC%98%81%EC%83%81%20%ED%91%9C%EC%8B%9C.md)

{% raw %}
이전: [04주차 결과 - Text-LCD 문자열 출력과 버튼 회전](04-result-text-lcd.md)  
다음: [05주차 결과 - TFT-LCD 타이밍 BRAM 색상 패턴](05-result-tft-lcd-bram.md)

## 핵심 요약

이번 예비보고서는 TFT-LCD의 구조와 구동 원리, 그리고 BRAM에 저장된 영상 데이터를 읽어 LCD에 표시하는 실습을 준비한다. 핵심은 RGB 데이터와 `HSYNC`, `VSYNC`, display enable 신호를 정확한 timing으로 생성해야 화면이 정상 출력된다는 점이다.

## 목표

- TFT-LCD의 active matrix 구조와 동작 원리를 이해한다.
- RGB color bit 수와 색 표현 원리를 이해한다.
- Row/Column driver와 controller의 역할을 구분한다.
- BRAM image와 color bar를 TFT-LCD에 출력하는 전체 흐름을 파악한다.

## LCD 종류

| 종류 | 방식 | 특징 |
|---|---|---|
| TN/STN | 수동 matrix | 구조가 단순하지만 흑백/저해상도, 시야각과 선명도 한계 |
| DSTN | STN 보정 | 명암과 색 보정 가능, 잔상과 낮은 선명도 |
| TFT-LCD | 능동 matrix | 픽셀마다 transistor 포함, 빠르고 선명함 |

TFT-LCD는 각 픽셀마다 transistor를 두어 직접 구동하는 active matrix 방식이다. 전력 소모가 작고 응답 속도가 빠르지만, 구조가 복잡하고 비용이 높다.

## TFT-LCD 구동 원리

액정 분자는 기본적으로 꼬인 배열을 갖는다. 전압이 없으면 빛이 액정을 따라 회전해 통과하고, 전압이 인가되면 배열이 변하여 빛의 통과량이 달라진다. 이 투과율을 RGB color filter와 결합해 색을 만든다.

RGB 각각의 bit 수가 많을수록 표현 가능한 밝기 단계가 증가한다. 실습에서는 일반적으로 RGB565 형태를 사용한다.

| 색 | bit 수 | 단계 |
|---|---:|---:|
| Red | 5bit | 32 |
| Green | 6bit | 64 |
| Blue | 5bit | 32 |

Green을 6비트로 더 많이 주는 이유는 사람 눈이 초록색 변화에 더 민감하기 때문이다.

## Row/Column Driver

- Row driver: gate line을 선택하여 특정 행의 TFT를 켠다.
- Column driver: 선택된 행의 각 픽셀에 실제 전압을 인가한다.
- Controller: 입력 영상 데이터를 driver가 사용할 수 있는 형식으로 바꾸고 timing 신호를 만든다.

## RPS-Z7020-TK TFT-LCD

보드에는 TM043NBH02 color TFT-LCD가 탑재되어 있다. RGB 신호와 `HSYNC`, `VSYNC` 신호를 통해 화면을 제어한다.

## 구동 로직

| 블록 | 역할 |
|---|---|
| Clock divider | 25MHz clock을 12.5MHz로 분주 |
| HSYNC Generator | horizontal timing과 `H_COUNT` 생성 |
| VSYNC Generator | vertical timing과 `V_COUNT` 생성 |
| BRAM Controller | BRAM address 생성, image pixel read |
| Color Bar Generator | test pattern 생성 |
| MUX | DIP switch에 따라 BRAM image 또는 color bar 선택 |

## 정리

5주차 예비의 핵심은 TFT-LCD 출력이 **픽셀 데이터 + 수평/수직 동기 + active video 구간** 의 조합이라는 점이다. BRAM에 저장된 영상은 단순히 읽기만 하면 되는 것이 아니라, 현재 `H_COUNT`, `V_COUNT`에 맞는 address를 계산하여 적절한 시점에 RGB로 내보내야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **05주차 예비 - TFT-LCD 구조와 BRAM 영상 표시**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
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
- **05주차 예비 - TFT-LCD 구조와 BRAM 영상 표시**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04주차 결과 - Text-LCD 문자열 출력과 버튼 회전](04-result-text-lcd.md) · 다음: [05주차 결과 - TFT-LCD 타이밍 BRAM 색상 패턴](05-result-tft-lcd-bram.md)
