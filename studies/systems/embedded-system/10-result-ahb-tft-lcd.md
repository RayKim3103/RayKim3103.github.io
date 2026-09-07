---
layout: page
title: "10주차 결과 - AHB TFT-LCD 이미지 출력"
permalink: /studies/systems/embedded-system/10-result-ahb-tft-lcd/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/10%EC%A3%BC%EC%B0%A8%20%EA%B2%B0%EA%B3%BC%20-%20AHB%20TFT-LCD%20%EC%9D%B4%EB%AF%B8%EC%A7%80%20%EC%B6%9C%EB%A0%A5.md)

{% raw %}
이전: [09주차 결과 - AXI Text-LCD PS PL 연동](09-result-axi-text-lcd-ps-pl.md)  
다음: [11주차 예비 - Push Button Interrupt System](11-prep-push-button-interrupt-system.md)

## 핵심 요약

이번 실습에서는 PS가 C 코드에서 image 배열을 읽고, memory mapped I/O 방식으로 PL의 BRAM에 image data를 전송하여 TFT-LCD에 출력한다. Zynq PS는 AXI 기반이지만 실습용 TFT-LCD 시스템은 AHB interface를 사용하므로 AXI-AHB Lite bridge가 필요하다.

## 전체 데이터 흐름

```text
C image array in PS
-> Xil_Out32()
-> AXI GP master
-> AXI Interconnect
-> AXI AHBLite Bridge
-> AHB2PORT1RAM
-> dual-port BRAM
-> TFTLCDCtrl / BRAMCtrl
-> RGB565 TFT-LCD output
```

## 주요 블록

| 블록 | 역할 |
|---|---|
| Zynq7 PS | image data 생성 및 register write |
| AXI Interconnect | AXI master/slave 연결과 address decoding |
| Processor System Reset | reset 안정화 |
| AXI AHBLite Bridge | AXI protocol을 AHB-Lite protocol로 변환 |
| `AHB2PORT1RAM` | AHB master와 dual-port BRAM 연결 |
| `register_set` | control register 제공 |
| `TFTLCDCtrl` | BRAM image 또는 color bar를 TFT-LCD에 출력 |

## 왜 AXI-AHB Bridge가 필요한가

Zynq PS의 GP master는 AXI interface를 사용한다. 하지만 이번 TFT-LCD용 PL 시스템은 AHB-Lite 기반으로 작성되어 있다. 두 protocol은 transaction 방식이 다르므로 bridge가 address, data, write/read control을 변환해야 한다.

## `top.v`의 변화

이전 TFT-LCD 실습과 비교해 `top.v`에는 AHB 관련 port와 모듈이 추가된다.

- `M_AHB_haddr`, `M_AHB_hwdata`, `M_AHB_hrdata`
- `M_AHB_hwrite`, `M_AHB_htrans`, `M_AHB_hsize`
- `AHB2PORT1RAM` 인스턴스
- `register_set` 인스턴스
- `TFTLCD_SW[0]`, `TFTLCD_SW[1]`를 control register bit에 연결

이전에는 `.coe`로 BRAM 초기값을 넣었지만, 이번에는 PS가 runtime에 image data를 BRAM에 write한다.

## `AHB2PORT1RAM`

이 모듈은 AHB-Lite transaction을 dual-port BRAM 접근으로 바꾼다.

| 기능 | 설명 |
|---|---|
| Address/Data phase 분리 | AHB write의 address phase와 data phase를 register로 맞춤 |
| `REQ[1]` 생성 | valid read/write transaction일 때 BRAM enable |
| `P1HADDRMUX` | write는 저장된 address, read는 현재 address 사용 |
| `HREADYOUT` | write 후 read 전환 시 충돌 방지 |
| Endian 처리 | `BIGEND`에 따라 하위 address bit 반전 |
| Byte write enable | byte/halfword/word 크기에 맞춰 `BWE1[3:0]` 생성 |

BRAM은 port B를 PS/AHB write용으로, port A를 TFT-LCD read용으로 사용한다.

## `register_set`

`register_set`은 AHB address에 따라 control register를 read/write한다.

| Address slice | register | 역할 |
|---|---|---|
| `HADDR[5:2] == 0` | `REG0` | image source 선택 |
| `HADDR[5:2] == 1` | `REG1` | image direction 선택 |

`HSIZE`에 따라 byte, halfword, word 접근을 처리한다. AXI/AHB data bus가 32비트이므로 작은 단위 접근은 address 하위 bit를 이용해 필요한 byte lane을 선택한다.

## 기본 C 코드

기본 application은 `hex`, `image0`, `image1` 세 이미지를 차례로 출력한다.

중요한 address 계산:

```c
XPAR_M_AHB_BASEADDR + y * 960 + x * 4
```

이유:

- TFT-LCD 가로 480 pixel
- RGB565 pixel 1개 = 16bit
- `Xil_Out32` 한 번에 32bit, 즉 2pixel write
- 한 줄은 480pixel = 240번 write
- 240번 x 4byte = 960byte

따라서 `x` loop는 0~239까지만 돈다.

## Quiz 이미지 구성

Quiz에서는 5가지 출력 상태를 구현했다.

| 번호 | 출력 | 구현 |
|---|---|---|
| 1 | RGB color bar | control register에 0 write |
| 2 | jet/village stripe 역방향 | 34 line마다 image source 교차, direction bit 설정 |
| 3 | 사람 이미지 좌측 절반 | x 0~119 image, 120~239 black |
| 4 | 사람 이미지 우측 절반 | x 0~119 black, 120~239 image |
| 5 | 사람 이미지 RGB inversion | `0xffffffff - packed_pixel` |

stripe는 272 line을 8개 band로 나누기 위해 `34 = 272/8`을 사용했다.

## Memory Mapped I/O

AHB는 memory mapped 방식이다. Processor는 I/O device와 memory를 별도 명령으로 구분하지 않고, 특정 address에 read/write하여 device register나 BRAM에 접근한다.

## 정리

10주차 결과의 핵심은 PS software가 `Xil_Out32`로 image data를 쓰면, interconnect와 bridge를 지나 PL BRAM에 저장되고, TFT-LCD controller가 그 BRAM을 읽어 화면으로 출력한다는 점이다. 이 구조는 embedded system에서 CPU, bus, memory, custom hardware가 협력하는 전형적인 예시다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **10주차 결과 - AHB TFT-LCD 이미지 출력**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
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
- **10주차 결과 - AHB TFT-LCD 이미지 출력**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [09주차 결과 - AXI Text-LCD PS PL 연동](09-result-axi-text-lcd-ps-pl.md) · 다음: [11주차 예비 - Push Button Interrupt System](11-prep-push-button-interrupt-system.md)
