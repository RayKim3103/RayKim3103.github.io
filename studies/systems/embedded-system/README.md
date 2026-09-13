---
layout: page
title: Embedded System
description: >
  Embedded System 강의 노트.
hide_description: false
sitemap: false
permalink: /studies/systems/embedded-system/
---

원본: [GitHub — Embedded System](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Embedded_System)

> Zynq FPGA(RPS-Z7020-TK) 기반 PS/PL 통합 실습 노트에 clock cycle·주파수·주소 계산을 실제 값까지 끝까지 추적하는 worked example(digit multiplexing 190.7Hz 유도, 1Hz clock divider 검증, LCD enable pulse 시간 환산, RGB565 색상 수 계산, frame rate 계산, AHB address stride 무충돌 검증, delay loop 비례식 검증, interrupt LED bit mask 계산, 64비트 register packing과 25MHz clock 역산 등)을 추가해 보강했습니다.

## 강의 노트

1. [02주차 예비 - Zynq PS PL 기초](02-prep-zynq-ps-pl.md) — PS/PL 역할 배분 예시
2. [02주차 결과 - Vivado PS PL LED 실습](02-result-vivado-ps-pl-led.md) — RegPushButton edge 검출 clock-by-clock 추적
3. [03주차 예비 - LED와 7-Segment 디지털 시계](03-prep-led-7-segment.md) — 32비트 segdata 폭 확인
4. [03주차 결과 - 7-Segment 시계와 전광판 구현](03-result-7-segment.md) — 190.7Hz digit multiplexing·1Hz clock divider 유도
5. [04주차 예비 - Text-LCD 컨트롤러와 타이밍](04-prep-text-lcd.md) — RS/R-W 조합표
6. [04주차 결과 - Text-LCD 문자열 출력과 버튼 회전](04-result-text-lcd.md) — enable pulse·전체 write 주기 시간 환산
7. [05주차 예비 - TFT-LCD 구조와 BRAM 영상 표시](05-prep-tft-lcd-bram.md) — RGB565 색상 수(65,536) 계산
8. [05주차 결과 - TFT-LCD 타이밍 BRAM 색상 패턴](05-result-tft-lcd-bram.md) — frame rate(≈83.25Hz) 계산
9. [06주차 예비 - TFT-LCD ASCII 문자 출력](06-prep-tft-lcd-ascii.md) — 문자 격자(60x34)·BRAM depth 여유 계산
10. [06주차 결과 - ASCII 문자 생성기와 화면 표시](06-result-ascii.md) — 55x33 실사용 비율(89%) 계산
11. [07주차 예비 - PS 기반 LED 7-Segment AXI 제어](07-prep-ps-led-7-segment-axi.md) — register 주소 간격 확인
12. [09주차 결과 - AXI Text-LCD PS PL 연동](09-result-axi-text-lcd-ps-pl.md) — delay loop 비례식 검증
13. [10주차 결과 - AHB TFT-LCD 이미지 출력](10-result-ahb-tft-lcd.md) — 행 간 address 무충돌 검증
14. [11주차 예비 - Push Button Interrupt System](11-prep-push-button-interrupt-system.md) — Polling vs Interrupt 비교
15. [11주차 결과 - PL Interrupt와 PS Handler](11-result-pl-interrupt-ps-handler.md) — LED bit mask 계산
16. [12주차 결과 - Zynq Ubuntu Root File System 구성](12-result-zynq-ubuntu-root-file-system.md) — partition boot 흐름 정리
17. [13주차 결과 - U-Boot Kernel Device Tree와 Driver 개념](13-result-u-boot-kernel-device-tree-driver.md) — uImage 크기 관계식
18. [14주차 예비 - Linux Device Driver와 Device Control](14-prep-linux-device-driver-control.md) — write() 호출 5단계 추적
19. [14주차 결과 - Sevenseg Driver 8-Byte Read Write](14-result-sevenseg-driver-8-byte-read-write.md) — 64비트 packing·25MHz clock 역산
