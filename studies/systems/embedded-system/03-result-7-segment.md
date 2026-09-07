---
layout: page
title: "03주차 결과 - 7-Segment 시계와 전광판 구현"
permalink: /studies/systems/embedded-system/03-result-7-segment/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/03%EC%A3%BC%EC%B0%A8%20%EA%B2%B0%EA%B3%BC%20-%207-Segment%20%EC%8B%9C%EA%B3%84%EC%99%80%20%EC%A0%84%EA%B4%91%ED%8C%90%20%EA%B5%AC%ED%98%84.md)

{% raw %}
이전: [03주차 예비 - LED와 7-Segment 디지털 시계](03-prep-led-7-segment.md)  
다음: [04주차 예비 - Text-LCD 컨트롤러와 타이밍](04-prep-text-lcd.md)

## 핵심 요약

이번 실습에서는 25MHz clock으로 1초 단위 시간 신호를 만들고, 7-segment 8자리에 `시-분-초`를 표시하는 회로를 구현했다. 추가로 초기 시간을 학번 기반 값으로 바꾸고, 실험 날짜를 1초마다 왼쪽으로 이동하는 전광판 형태로 출력했다.

## 기본 모듈 역할

| 모듈 | 핵심 역할 |
|---|---|
| `bin2seg.v` | 4비트 입력을 7-segment pattern으로 변환 |
| `seven_seg.v` | 32비트 `segdata`를 8자리 display로 multiplexing |
| `clock.v` | 25MHz clock을 1초, 10초, 1분, 10분, 1시간 단위로 누적 |
| `top.v` | `clock`과 `seven_seg`를 연결하여 보드 출력 생성 |

## `bin2seg.v`

`bin2seg`는 `bin_data` 값에 따라 segment pattern을 선택한다. 숫자 0~9와 `-` 표시용 `4'b1010`이 case로 정의된다. 단, 내부 pattern은 cathode형 기준이므로 Anode형 보드에 출력할 때는 `~seg` 형태로 반전되어야 한다.

## `seven_seg.v`

`seven_seg`는 다음 일을 한다.

- 32비트 `data`를 4비트씩 8개로 분리한다.
- 각 4비트를 `bin2seg`에 넣어 `seg1~seg8` pattern으로 변환한다.
- `clk_cnt`가 16384 cycle마다 `com_cnt`를 증가시킨다.
- `com_cnt` 값에 따라 하나의 digit만 선택하고, 해당 segment pattern을 `segout`으로 출력한다.

25MHz에서 `25MHz / (16384 * 8) ≈ 190Hz`로 전체 digit이 순환하므로 사람이 보기에 연속적으로 켜진 것처럼 보인다.

## `clock.v`

`clock.v`는 25MHz 입력 clock에서 1Hz 신호를 만든 뒤 초, 분, 시간을 누적한다.

- `cnt == 12,499,999`일 때 `onesec_clk`를 toggle하여 1초 주기 신호 생성
- `onesec_cnt`: 1초 자리, 0~9
- `tensec_cnt`: 10초 자리, 0~5
- `onemin_cnt`: 1분 자리, 0~9
- `tenmin_cnt`: 10분 자리, 0~5
- `hour_cnt`: 시간 자리, 0~12 범위 처리

`segdata`는 8개 digit에 대응하는 32비트이며, 구분자 `-`는 `4'b1010`으로 표현된다.

## 기본 시계 검증

보드에서는 초기값 `12-00-00`이 표시되고, 1초, 10초, 1분, 10분, 시간 단위가 순차적으로 바뀌는 것을 확인했다. reset 시에는 clock 관련 register가 초기화되어 동작이 멈춘 상태로 보이며, 이를 통해 reset 동작도 검증했다.

## Quiz 1: 초기 시각 변경

목표는 분과 초를 학번 뒤 4자리로 바꾸어 `12-20-87`을 초기값으로 표시하는 것이다.

수정 포인트:

- `clock.v`에서 reset 시 `segdata` 초기값 변경
- 표시값뿐 아니라 내부 counter 값도 다음 초가 자연스럽게 이어지도록 변경
- 예를 들어 1초 자리를 7로 표시하려면 다음 tick에서 8이 되도록 `onesec_cnt` 흐름을 맞춘다.

10초 자리에 8을 사용하는 경우 일반적인 0~5 범위를 벗어나므로, 기존 counter 조건과 실제 동작의 관계를 주의해야 한다.

## Quiz 2: 날짜 전광판

목표는 실험 날짜 `20250321`을 표시하고, 1초마다 한 칸씩 왼쪽으로 이동한 뒤 오른쪽에서 다시 나타나게 하는 것이다.

구현 방식:

- `clock.v`에서 1초 clock 생성 부분만 활용
- 다른 시간 누적 always block은 사용하지 않음
- reset 시 `segdata`를 4비트씩 나누어 `2,0,2,5,0,3,2,1`로 설정
- 매 `onesec_clk`마다 `segdata[31:28] <= segdata[27:24]`처럼 4비트 단위로 순환 이동

## 개선 및 고찰

- `4'hb0000`처럼 폭 표기와 값 표기가 어색한 코드는 `{4'b0000, hour_cnt}`처럼 명확히 작성하는 편이 좋다.
- 전광판 shift는 4비트 slice를 하나씩 직접 대입하는 방식보다 concatenation을 이용하면 더 간단해질 수 있다.
- 190Hz multiplexing은 충분히 빠르므로, 더 낮은 refresh로도 flicker 없이 전력 소모를 줄일 여지가 있다.

## 정리

3주차 결과의 핵심은 **시간 생성 로직** 과 **표시 로직** 을 분리한 점이다. `clock.v`는 값을 만들고, `seven_seg.v`는 그 값을 사람이 볼 수 있게 빠르게 순환 표시한다. Quiz는 결국 `segdata`를 어떤 규칙으로 갱신할지 바꾸는 응용이다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03주차 결과 - 7-Segment 시계와 전광판 구현**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
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
- **03주차 결과 - 7-Segment 시계와 전광판 구현**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03주차 예비 - LED와 7-Segment 디지털 시계](03-prep-led-7-segment.md) · 다음: [04주차 예비 - Text-LCD 컨트롤러와 타이밍](04-prep-text-lcd.md)
