---
layout: page
title: "14주차 결과 - Sevenseg Driver 8-Byte Read Write"
permalink: /studies/systems/embedded-system/14-result-sevenseg-driver-8-byte-read-write/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/14%EC%A3%BC%EC%B0%A8%20%EA%B2%B0%EA%B3%BC%20-%20Sevenseg%20Driver%208-Byte%20Read%20Write.md)

{% raw %}
이전: [14주차 예비 - Linux Device Driver와 Device Control](14-prep-linux-device-driver-control.md)  
다음: 없음

## 핵심 요약

이번 실습에서는 Linux device driver를 사용하여 sevenseg IP와 LED를 제어했다. 결과적으로 user application이 `/dev/zynq_sevenseg`를 열고 4Byte 또는 8Byte 데이터를 write하면, driver가 이를 kernel buffer와 mapped device address를 통해 PL hardware에 전달한다.

## 전체 제어 원리

```text
User application
-> /dev/zynq_sevenseg
-> system call
-> sevenseg_fops
-> sevenseg_driver
-> sevenseg_virtual_addr
-> AXI interconnect
-> sevenseg IP
-> 7-segment + LED
```

Software는 file descriptor와 system call만 사용하고, hardware address 접근은 device driver가 담당한다.

## `seven_seg` 모듈 LED/7-Segment 제어

`seven_seg` 모듈은 두 종류의 출력을 처리한다.

| 입력 | 출력 | 설명 |
|---|---|---|
| `data[31:0]` | 7-segment | 4비트씩 8개 digit 표시 |
| `leddata[31:0]` | LED | 8비트씩 4개 pattern을 시간 순서로 출력 |

### LED 출력

- `clk_led`가 50,000,000 cycle에 도달하면 0으로 reset
- `led_cnt`가 0~3 순환
- `led_cnt`에 따라 `leddata[7:0]`, `[15:8]`, `[23:16]`, `[31:24]` 중 하나를 `led_out`으로 출력
- 각 pattern은 약 2초 간격으로 바뀐다.

### 7-Segment 출력

- `data[31:0]`를 4비트씩 나눠 `bin2seg`로 변환
- `clk_cnt`가 16384 cycle에 도달할 때마다 `com_cnt` 증가
- `com_cnt`에 따라 8개 digit을 순환 선택
- Anode형 display에 맞춰 `segout <= ~segN`

## Device Driver 구조

| 구성 | 역할 |
|---|---|
| `sevenseg_fops` | `open`, `release`, `write`, `read` 함수 mapping |
| `sevenseg_init` | driver 등록, physical address mapping, buffer 할당 |
| `sevenseg_exit` | mapping 해제, driver 등록 제거, buffer 해제 |
| `sevenseg_open` | device open 시 log 출력 |
| `sevenseg_close` | device close 시 log 출력 |
| `sevenseg_write` | user data를 device로 write |
| `sevenseg_read` | device data를 user로 read |

## Driver 초기화와 종료

`sevenseg_init`:

- `register_chrdev`로 major number와 device name 등록
- `ioremap`으로 physical device address를 kernel virtual address로 mapping
- `kmalloc`으로 `device_buffer` 할당

`sevenseg_exit`:

- `iounmap`으로 virtual address mapping 해제
- `unregister_chrdev`로 character device 등록 제거
- `kfree`로 buffer 해제

## 4Byte와 8Byte 처리

`sevenseg_write`는 length가 `sizeof(unsigned)` 또는 `sizeof(unsigned long long)`인지 검사한다.

- 4Byte: `volatile unsigned *`로 casting하여 write
- 8Byte: `volatile unsigned long long *`로 casting하여 write

`sevenseg_read`도 같은 방식으로 4Byte 또는 8Byte 값을 device address에서 읽어 `device_buffer`에 저장하고, `copy_to_user`로 user buffer에 전달한다.

핵심 구현 코드:

```c
*(volatile unsigned long long*)sevenseg_virtual_addr =
    *(volatile unsigned long long*)device_buffer;

*(volatile unsigned long long*)device_buffer =
    *(volatile unsigned long long*)sevenseg_virtual_addr;
```

## `sevenseg_test.c`

Application은 다음 값을 사용했다.

| 값 | 의미 |
|---|---|
| `SEG_VALUE = 0x20872186` | 7-segment에 표시할 학번 뒤 8자리 |
| `LED_VALUE = 0x87654321` | LED가 8비트씩 순환 출력할 pattern |

8Byte write value는 LED와 sevenseg 값을 하나로 합쳐 만든다.

```c
wr_long_value = ((unsigned long long)LED_VALUE << 32LLU) | SEG_VALUE;
```

상위 32비트는 LED pattern, 하위 32비트는 sevenseg 표시값으로 사용된다.

## Hex 상수와 형 변환 고찰

`0x87654321`은 signed int 범위에서 음수처럼 해석될 수 있다. 이 상태로 32bit shift를 하면 sign extension이나 overflow 문제가 생길 수 있으므로, `unsigned long long`으로 명확히 casting해야 한다.

반대로 decimal 값으로 매우 큰 수를 직접 쓰면 compiler가 더 큰 unsigned type으로 승격하여 처리할 수 있지만, 의도를 명확히 하려면 casting을 사용하는 편이 안전하다.

## 실험 결과 해석

LED는 `0x87654321`의 하위 byte부터 순서대로 출력되므로, `21 -> 43 -> 65 -> 87` 형태의 8비트 pattern이 시간에 따라 반복된다. 7-segment에는 `0x20872186`이 4비트 단위 digit으로 표시된다.

## 정리

14주차 결과의 핵심은 Linux application에서 시작된 8Byte write가 driver, kernel buffer, virtual address, AXI interconnect를 거쳐 PL hardware 출력으로 이어지는 전 과정을 확인한 것이다. 이 실습은 embedded Linux에서 custom IP를 device driver로 제어하는 전체 패턴을 완성한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **14주차 결과 - Sevenseg Driver 8-Byte Read Write**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
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
- **14주차 결과 - Sevenseg Driver 8-Byte Read Write**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [14주차 예비 - Linux Device Driver와 Device Control](14-prep-linux-device-driver-control.md)
