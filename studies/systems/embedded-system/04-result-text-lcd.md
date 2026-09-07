---
layout: page
title: "04주차 결과 - Text-LCD 문자열 출력과 버튼 회전"
permalink: /studies/systems/embedded-system/04-result-text-lcd/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/04%EC%A3%BC%EC%B0%A8%20%EA%B2%B0%EA%B3%BC%20-%20Text-LCD%20%EB%AC%B8%EC%9E%90%EC%97%B4%20%EC%B6%9C%EB%A0%A5%EA%B3%BC%20%EB%B2%84%ED%8A%BC%20%ED%9A%8C%EC%A0%84.md)

{% raw %}
이전: [04주차 예비 - Text-LCD 컨트롤러와 타이밍](04-prep-text-lcd.md)  
다음: [05주차 예비 - TFT-LCD 구조와 BRAM 영상 표시](05-prep-tft-lcd-bram.md)

## 핵심 요약

이번 실습에서는 Text-LCD controller RTL을 분석하고, LCD 2행 16문자 출력이 어떤 timing과 mode sequence로 이루어지는지 확인했다. Quiz에서는 버튼 입력에 따라 문자열을 1/4 단위로 좌우 회전하거나 새로운 메시지로 교체하도록 수정했다.

## `text_lcd.v` 구조

| 구성 | 역할 |
|---|---|
| 문자 macro | 각 문자에 대응하는 8비트 ASCII-like 값 정의 |
| `reg_a~reg_h` | 32비트씩 총 8개, 4문자 단위 문자열 저장 |
| `delay_lcdclk` | LCD enable pulse timing 생성 |
| `count_lcd` | LCD sequence 진행 counter |
| `lcd_mode` | 현재 LCD 동작 mode |
| `set_data[9:0]` | `{RS, R/W, DB[7:0]}` 묶음 |

`reg_a~reg_d`는 1행 16문자, `reg_e~reg_h`는 2행 16문자를 담당한다. 1문자는 8비트이므로 32비트 register 하나가 4문자를 담는다.

## Counter와 Mode Sequence

`delay_lcdclk`는 0~1999를 반복한다. 이 counter가 특정 값일 때 `lcd_en`을 제어한다.

- `delay_lcdclk == 200`: `lcd_en = 1`
- `delay_lcdclk == 1800`: `lcd_en = 0`

`count_lcd`는 `delay_lcdclk == 0`일 때 증가하며, LCD 초기화와 문자 쓰기 순서를 만든다.

| `count_lcd` | mode | 동작 |
|---|---|---|
| 0 | `mode_pwron` | 전원 설정 |
| 1 | `mode_fnset` | function set |
| 2 | `mode_onoff` | display on/off |
| 3~5 | `mode_entr*` | entry/home/clear |
| 6 | `mode_seta1` | 1행 DDRAM address 설정 |
| 7~22 | `mode_wr1st` | 1행 16문자 write |
| 23 | `mode_seta2` | 2행 DDRAM address 설정 |
| 24~39 | `mode_wr2nd` | 2행 16문자 write |
| 40 | `mode_delay` | 안정화 지연 |

초기 설정 이후에는 `count_lcd`가 6으로 돌아가므로, 1행/2행 쓰기 sequence를 반복하며 화면을 갱신한다.

## `top.v`

`top.v`는 Text-LCD 관련 6개 포트만 실제로 사용한다.

- `resetn`
- `lcdclk`
- `lcd_rs`
- `lcd_rw`
- `lcd_en`
- `lcd_data[7:0]`

Processor System 관련 포트가 포함되어 있으나 이번 실습에서는 PL 단독 Text-LCD 구동이므로 실질적으로 사용되지 않는다.

## Quiz: 버튼 기반 문자열 회전

수정 목표:

- Button 0: 각 행의 문자열을 1/4만큼 right rotate
- Button 1: 각 행의 문자열을 1/4만큼 left rotate
- Button 2: 문자열을 `"MESSAGE         ROTATION        "`으로 교체

수정 포인트:

- 버튼 입력을 받기 위해 `PushButton[2:0]` 포트 추가
- `reg_a~reg_h`를 `wire`에서 `reg`로 변경
- 버튼 edge 검출을 위해 `RegPushButton` 추가
- reset 시 `"2025 EMBEDDED    SYSTEM LAB      "` 초기화

1행과 2행을 따로 회전시킨 이유는 `reg_a~reg_d`가 1행, `reg_e~reg_h`가 2행을 담당하기 때문이다. 16문자를 4문자 단위로 이동하므로 1/4 회전이 된다.

## Debouncer 고찰

빠른 버튼 입력이나 불안정한 접점 때문에 LCD 문자가 깨지는 문제가 있었다. 이를 줄이기 위해 debouncer를 고려했다. Debouncer는 버튼 입력을 flip-flop으로 안정화하고, 유효한 edge만 짧게 만들어 노이즈 영향을 줄인다.

다만 적용 과정에서 reset 후 문자열이 의도치 않게 1/4 right rotate되는 문제가 발생했다. 가능한 원인은 다음과 같이 정리된다.

- 실제 보드에서 glitch 또는 clock skew로 timing constraint가 깨졌을 가능성
- decoder output always block의 sensitivity 또는 reset 반영 방식이 불완전했을 가능성
- debouncer 인스턴스 위치와 reset sequence가 LCD 초기화 sequence와 충돌했을 가능성

개선 방향은 debouncer를 Text-LCD 모듈 내부 clock domain에 맞춰 인스턴스화하고, reset edge가 decoder output에도 즉시 반영되도록 always 조건을 보완하는 것이다.

## 정리

4주차 결과의 핵심은 Text-LCD 출력이 단순 문자열 대입이 아니라, **DDRAM address 설정, 문자 단위 write, enable timing, mode sequence** 의 조합이라는 점이다. 버튼 회전 Quiz는 이 출력 데이터 저장부(`reg_a~reg_h`)를 동적으로 바꾸는 응용이다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04주차 결과 - Text-LCD 문자열 출력과 버튼 회전**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
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
- **04주차 결과 - Text-LCD 문자열 출력과 버튼 회전**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04주차 예비 - Text-LCD 컨트롤러와 타이밍](04-prep-text-lcd.md) · 다음: [05주차 예비 - TFT-LCD 구조와 BRAM 영상 표시](05-prep-tft-lcd-bram.md)
