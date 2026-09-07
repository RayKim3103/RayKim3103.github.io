---
layout: page
title: "04주차 예비 - Text-LCD 컨트롤러와 타이밍"
permalink: /studies/systems/embedded-system/04-prep-text-lcd/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/04%EC%A3%BC%EC%B0%A8%20%EC%98%88%EB%B9%84%20-%20Text-LCD%20%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%EC%99%80%20%ED%83%80%EC%9D%B4%EB%B0%8D.md)

{% raw %}
이전: [03주차 결과 - 7-Segment 시계와 전광판 구현](03-result-7-segment.md)  
다음: [04주차 결과 - Text-LCD 문자열 출력과 버튼 회전](04-result-text-lcd.md)

## 핵심 요약

이번 예비보고서는 Text-LCD 컨트롤러 설계를 준비한다. 핵심은 LCD 모듈이 단순한 LED 배열이 아니라 내부 controller와 DDRAM/CGRAM을 가진 장치이며, `RS`, `R/W`, `E`, `DB[7:0]` 신호를 데이터시트의 timing constraint에 맞춰 제어해야 한다는 점이다.

## 목표

- LCD와 Text-LCD의 구조를 이해한다.
- LCD 제어 코드와 DDRAM address 구조를 이해한다.
- Character LCD write/read timing diagram을 해석한다.
- RPS-Z7020-TK 보드에서 JTAG 모드로 PL 회로를 검증하는 절차를 확인한다.

## LCD와 Text-LCD

LCD는 전압에 따라 액정 분자의 배열이 변하고, 그 결과 빛의 투과가 달라지는 원리를 이용한다. Text-LCD는 LCD 패널과 제어기가 결합된 모듈이므로, 설계자는 픽셀을 직접 제어하기보다 controller에 명령어와 문자 데이터를 전송한다.

## LCD 인터페이스 신호

| 신호 | 역할 |
|---|---|
| `DB0~DB7` | 8비트 데이터 버스 |
| `E` | Enable, 데이터 전송 타이밍 제어 |
| `R/W` | 읽기/쓰기 선택 |
| `RS` | 명령 register와 data memory 선택 |
| `Vdd`, `Vss` | 전원 |

`RS=0`이면 명령어 또는 busy flag/address 관련 접근이고, `RS=1`이면 DDRAM/CGRAM 데이터 접근이다. `R/W=0`은 write, `R/W=1`은 read를 의미한다.

## 주요 LCD 명령

| 명령 범주 | 기능 |
|---|---|
| Entry mode set | 문자 입력 후 address 증가/감소, display shift 여부 설정 |
| Display on/off | 화면, 커서, blink on/off |
| Cursor/display shift | 커서 또는 화면을 좌우 이동 |
| Function set | 8비트/4비트, 1행/2행, font 크기 설정 |
| CGRAM address set | 사용자 문자 생성용 메모리 주소 설정 |
| DDRAM address set | 화면에 표시될 문자 메모리 주소 설정 |
| Data write/read | CGRAM 또는 DDRAM에 문자 데이터 접근 |

## Text-LCD 동작 타이밍

LCD는 write/read 동작에서 setup time, hold time, enable pulse width 등을 만족해야 한다. 따라서 Verilog 코드에서는 단순히 값을 바로 바꾸는 것이 아니라 counter를 이용해 충분한 delay를 두고 `lcd_en`을 High/Low로 전환해야 한다.

## 구동 회로 블록

Text-LCD 구동 회로는 다음 흐름으로 구성된다.

1. 입력 clock을 counter x2000에 넣어 LCD enable timing을 만든다.
2. counter x40이 LCD 동작 단계, 즉 mode를 진행시킨다.
3. mode decoder가 전원 설정, function set, address set, write 동작을 순서대로 선택한다.
4. decoder output이 `RS`, `R/W`, `DB[7:0]`로 나간다.

## 보드 검증 조건

Zynq 보드는 Boot/JTAG mode switch로 부팅 방식을 선택한다. 이번 실습에서는 T-flash를 사용하지 않고 Cascaded JTAG로 PL을 다운로드한다.

확인 사항:

- BOOT_MODE[3]에 해당하는 J19 점퍼를 2-3 연결
- T-flash 제거
- JTAG 연결 후 bitstream 다운로드

## 정리

4주차 예비의 핵심은 Text-LCD가 내부 controller를 가진 장치이므로, 문자 데이터뿐 아니라 **명령 순서와 timing** 이 설계의 중심이라는 점이다. 다음 결과 실습에서는 이 개념이 `lcd_mode`, `count_lcd`, `set_data`로 구현된다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04주차 예비 - Text-LCD 컨트롤러와 타이밍**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
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
- **04주차 예비 - Text-LCD 컨트롤러와 타이밍**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03주차 결과 - 7-Segment 시계와 전광판 구현](03-result-7-segment.md) · 다음: [04주차 결과 - Text-LCD 문자열 출력과 버튼 회전](04-result-text-lcd.md)
