---
layout: page
title: "05주차 결과 - TFT-LCD 타이밍 BRAM 색상 패턴"
permalink: /studies/systems/embedded-system/05-result-tft-lcd-bram/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/05%EC%A3%BC%EC%B0%A8%20%EA%B2%B0%EA%B3%BC%20-%20TFT-LCD%20%ED%83%80%EC%9D%B4%EB%B0%8D%20BRAM%20%EC%83%89%EC%83%81%20%ED%8C%A8%ED%84%B4.md)

{% raw %}
이전: [05주차 예비 - TFT-LCD 구조와 BRAM 영상 표시](05-prep-tft-lcd-bram.md)  
다음: [06주차 예비 - TFT-LCD ASCII 문자 출력](06-prep-tft-lcd-ascii.md)

## 핵심 요약

이번 실습에서는 TFT-LCD에 color bar와 BRAM image를 출력하는 전체 RTL을 분석했다. `horizontal.v`, `vertical.v`가 display timing을 만들고, `rgb.v` 또는 `BRAMCtrl.v`가 pixel RGB 값을 공급한다. Quiz에서는 DIP switch로 여러 image pattern을 선택하고, button으로 RGB inversion을 toggle하도록 확장했다.

## Horizontal Timing

`horizontal.v`는 12.5MHz pixel clock 기준으로 한 줄의 수평 timing을 만든다.

| 구간 | cycle | 신호 |
|---|---:|---|
| Sync pulse | 41 | `Hsync=0`, `hDE=0` |
| Back porch | 2 | `Hsync=1`, `hDE=0` |
| Active video | 480 | `Hsync=1`, `hDE=1` |
| Front porch | 2 | `Hsync=1`, `hDE=0` |
| 총합 | 525 | `H_COUNT` 0~524 |

`UP_CLKa`는 한 줄이 끝났음을 vertical timing에 알려주는 신호로 사용된다.

## Vertical Timing

`vertical.v`는 한 frame의 세로 timing을 만든다.

| 구간 | line | 신호 |
|---|---:|---|
| Sync pulse | 10 | `Vsync=0`, `vDE=0` |
| Back porch | 2 | `Vsync=1`, `vDE=0` |
| Active video | 272 | `Vsync=1`, `vDE=1` |
| Front porch | 2 | `Vsync=1`, `vDE=0` |
| 총합 | 286 | `V_COUNT` 0~285 |

최종 active display enable은 `DEimage = hDE & vDE`로 만든다.

## Color Bar

`rgb.v`는 `V_COUNT` 범위에 따라 색을 바꾸어 수평 color bar를 만든다. 예를 들어 특정 vertical range마다 white, yellow, cyan, green, purple, red, blue, white를 출력한다.

색상은 RGB565 형태다.

- White: R/G/B 모두 최대
- Yellow: R, G 최대, B 0
- Cyan: G, B 최대, R 0
- Red: R 최대, G/B 0
- Blue: B 최대, R/G 0

## Clock Divider

`g2m.v`는 입력 clock의 posedge마다 출력 clock을 toggle한다. 따라서 frequency가 절반이 된다. TFT-LCD가 요구하는 pixel clock에 맞추기 위한 단순 divider다.

## BRAM Controller

`BRAMCtrl.v`는 현재 화면 좌표를 BRAM address로 변환한다.

| 신호 | 역할 |
|---|---|
| `hcnt` | active video 구간의 horizontal pixel index |
| `vcnt` | 현재 line의 시작 address |
| `BRAMADDR` | `vcnt + hcnt` |
| `BRAMDATA[15:0]` | RGB565 pixel data |
| `Reverse_SW` | 위에서 아래/아래에서 위로 읽는 방향 선택 |

정방향은 `Vsync`가 blank 구간일 때 `vcnt=0`에서 시작하고, 한 줄이 끝날 때마다 `HSIZE`만큼 증가한다. 반전 출력은 마지막 line address에서 시작하여 한 줄마다 `HSIZE`만큼 감소한다.

## `TFTLCDCtrl.v`

이 모듈은 전체 TFT-LCD 출력의 중심이다.

- clock divider 인스턴스
- horizontal/vertical timing generator 인스턴스
- color bar generator 인스턴스
- BRAM controller 인스턴스
- switch에 따른 RGB source 선택
- reset 시 `Hsync`, `Vsync` 초기화
- `Tpower=1`로 backlight on

## Quiz 확장

추가 구현:

- DIP switch로 출력 source 선택
- horizontal half color bar (`H_half`)
- vertical half color bar (`V_half`)
- red/green gradient (`Grad`)
- BRAM image
- push button으로 RGB inversion toggle

MUX는 switch 우선순위에 따라 `BRAM_R/G/B`, `H_R/G/B`, `V_R/G/B`, `G_R/G/B` 중 하나를 선택한다. RGB inversion은 선택된 RGB 값에서 최대값을 빼는 방식이다.

```verilog
reg_R = 5'b11111 - wire_R;
reg_G = 6'b111111 - wire_G;
reg_B = 5'b11111 - wire_B;
```

## 고찰

- Skeleton code에서 Blue bit width가 6bit로 처리된 부분은 RGB565와 맞지 않으므로 5bit로 수정해야 한다.
- 사용하지 않는 AHB/PS 관련 코드가 `top.v`에 남아 있으면 가독성과 합성 결과 해석이 어려워진다.
- `H_COUNT`, `V_COUNT` 범위를 정할 때 sync pulse와 back porch offset을 고려해야 실제 화면 위치가 정확하다.

## 정리

5주차 결과의 핵심은 video output을 **timing generator + pixel generator + memory address generator + RGB source selector** 로 분해해 이해한 것이다. 이 구조는 이후 PS가 image data를 BRAM에 쓰는 실습의 기반이 된다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **05주차 결과 - TFT-LCD 타이밍 BRAM 색상 패턴**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
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
- **05주차 결과 - TFT-LCD 타이밍 BRAM 색상 패턴**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [05주차 예비 - TFT-LCD 구조와 BRAM 영상 표시](05-prep-tft-lcd-bram.md) · 다음: [06주차 예비 - TFT-LCD ASCII 문자 출력](06-prep-tft-lcd-ascii.md)
