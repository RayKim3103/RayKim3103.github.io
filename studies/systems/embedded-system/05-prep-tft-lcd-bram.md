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

## RGB565 색상 단계 수 계산

RGB565가 표현 가능한 전체 색 수는 각 색의 단계 수를 곱해서 구한다.

$$
2^5 (\text{Red}) \times 2^6 (\text{Green}) \times 2^5 (\text{Blue}) = 32 \times 64 \times 32 = 65{,}536 = 2^{16}
$$

비트 수를 직접 더해도 $$5+6+5=16$$비트로 같은 결과가 나온다. Green에 1비트를 더 준 이유(사람 눈이 초록 변화에 더 민감)가 실제로는 "같은 16비트 예산 안에서 지각적으로 가장 유리하게 배분한 것"임을 숫자로 확인할 수 있다 — 만약 5/5/6(R/G/B)처럼 Blue에 더 준다면 색 수는 동일하게 65,536이지만 사람이 체감하는 밝기 계조는 더 나빠진다.

## Clock Divider 예습

구동 로직 표의 "25MHz clock을 12.5MHz로 분주"는 [05주차 결과](05-result-tft-lcd-bram.md)의 `g2m.v`(posedge마다 toggle)로 구현된다. 주기로 보면 $$1/25\text{MHz}=40\text{ns}$$에서 $$1/12.5\text{MHz}=80\text{ns}$$로, 주기가 정확히 2배가 된다.

## 복습 질문

- RGB565에서 Red/Green/Blue 각각의 단계 수를 곱해 전체 색 수 65,536을 직접 계산할 수 있는가?
- Green에 1비트를 더 주는 설계가 "같은 총 비트 수" 안에서 어떤 trade-off인지 설명할 수 있는가?
- 25MHz를 12.5MHz로 나누는 `g2m.v`의 toggle 방식이 주기를 몇 배로 만드는지 설명할 수 있는가?

## 정리

5주차 예비의 핵심은 TFT-LCD 출력이 **픽셀 데이터 + 수평/수직 동기 + active video 구간** 의 조합이라는 점이다. BRAM에 저장된 영상은 단순히 읽기만 하면 되는 것이 아니라, 현재 `H_COUNT`, `V_COUNT`에 맞는 address를 계산하여 적절한 시점에 RGB로 내보내야 한다.

{% endraw %}

---

이전: [04주차 결과 - Text-LCD 문자열 출력과 버튼 회전](04-result-text-lcd.md) · 다음: [05주차 결과 - TFT-LCD 타이밍 BRAM 색상 패턴](05-result-tft-lcd-bram.md)
