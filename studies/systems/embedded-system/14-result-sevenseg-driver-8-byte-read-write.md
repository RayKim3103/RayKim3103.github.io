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

## 숫자로 확인하기 — 64비트 write 값과 clock 주파수 역산

**64비트 write 값 조립**: `wr_long_value = ((unsigned long long)LED_VALUE << 32LLU) | SEG_VALUE`에 실제 값을 대입하면

$$
(0\text{x}87654321 \ll 32) \,|\, 0\text{x}20872186 = 0\text{x}8765432120872186
$$

상위 32비트(`0x87654321`)가 LED 값, 하위 32비트(`0x20872186`)가 7-segment 값으로 하나의 64bit word 안에 나란히 들어간다 — `sevenseg_write`가 8byte 단위로 한 번에 write할 수 있는 이유가 바로 이 word 안에 두 device의 값이 함께 packing되어 있기 때문이다.

**LED 2초 간격에서 driving clock 역산**: `clk_led`가 50,000,000 cycle마다 pattern이 바뀌고 그 간격이 "약 2초"로 관찰되었다. 이로부터 driving clock 주파수를 역산하면

$$
\frac{50{,}000{,}000\text{ cycle}}{2\text{s}} = 25{,}000{,}000\text{Hz} = 25\text{MHz}
$$

이 값은 2, 3, 4주차에서부터 계속 사용된 PL clock 25MHz와 정확히 일치한다 — 14주차의 sevenseg RTL도 같은 25MHz clock domain에서 동작하고 있음을 이 역산으로 확인할 수 있다.

**7-segment multiplexing 주파수도 동일하게 재현**: `clk_cnt`가 16384에 도달할 때마다 `com_cnt`가 증가하는 구조는 3주차 `seven_seg.v`와 동일하다. 같은 25MHz clock을 가정하면 3주차에서 유도한 것과 똑같이

$$
\frac{25{,}000{,}000}{16384 \times 8} \approx 190.7\text{Hz}
$$

의 digit 순환 주파수가 나온다([3주차 결과](03-result-7-segment.md)의 계산과 동일). 서로 다른 주차, 다른 실습 목적(RTL 단독 vs Linux driver 경유)이지만 근본 hardware multiplexing 원리는 동일하게 유지된다는 것을 확인할 수 있다.

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

## 복습 질문

- `(LED_VALUE << 32) | SEG_VALUE` 계산으로 `0x8765432120872186`이 나오는 과정을 직접 재현할 수 있는가?
- `clk_led`의 50,000,000 cycle과 관찰된 "약 2초" 간격으로부터 driving clock이 25MHz임을 역산할 수 있는가?
- 14주차의 sevenseg multiplexing 주파수(≈190.7Hz)가 3주차와 같은 값으로 나오는 이유를, 두 주차의 회로 구조 차이(RTL 단독 vs Linux driver 경유)와 함께 설명할 수 있는가?
- `0x87654321`을 signed int로 잘못 다루면 어떤 문제가 생길 수 있는지, `unsigned long long` casting이 왜 필요한지 설명할 수 있는가?

## 정리

14주차 결과의 핵심은 Linux application에서 시작된 8Byte write가 driver, kernel buffer, virtual address, AXI interconnect를 거쳐 PL hardware 출력으로 이어지는 전 과정을 확인한 것이다. 이 실습은 embedded Linux에서 custom IP를 device driver로 제어하는 전체 패턴을 완성한다.

{% endraw %}

---

이전: [14주차 예비 - Linux Device Driver와 Device Control](14-prep-linux-device-driver-control.md)
