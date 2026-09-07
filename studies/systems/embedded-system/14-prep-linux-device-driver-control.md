---
layout: page
title: "14주차 예비 - Linux Device Driver와 Device Control"
permalink: /studies/systems/embedded-system/14-prep-linux-device-driver-control/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/14%EC%A3%BC%EC%B0%A8%20%EC%98%88%EB%B9%84%20-%20Linux%20Device%20Driver%EC%99%80%20Device%20Control.md)

{% raw %}
이전: [13주차 결과 - U-Boot Kernel Device Tree와 Driver 개념](13-result-u-boot-kernel-device-tree-driver.md)  
다음: [14주차 결과 - Sevenseg Driver 8-Byte Read Write](14-result-sevenseg-driver-8-byte-read-write.md)

## 핵심 요약

이번 예비보고서는 Linux booting 후 application이 device driver를 통해 실제 hardware를 제어하는 전체 흐름을 정리한다. 핵심은 user application이 직접 hardware address를 만지는 것이 아니라, device file과 system call을 통해 kernel driver에 요청하고, driver가 mapped virtual address로 PL device를 제어한다는 점이다.

## Linux Booting 요소

| 요소 | 역할 |
|---|---|
| Bootloader | hardware 초기화, device tree와 kernel image load |
| Kernel image | process, memory, network, device resource 관리 |
| Device tree | hardware 구조, address, size, interrupt 정보 전달 |
| File system | command, library, config, application, device file 제공 |

## 전체 제어 흐름

```text
User Application
-> open("/dev/zynq_sevenseg")
-> file descriptor 획득
-> write/read system call
-> device file
-> chrdev[] major number lookup
-> device_fops
-> device driver
-> copy_from_user / copy_to_user
-> ioremap된 virtual address
-> AXI interconnect
-> PL sevenseg IP
-> 7-segment / LED
```

## File Descriptor

각 process는 file descriptor table을 가진다. `open()`을 호출하면 비어 있는 index에 file object가 연결되고, 그 index가 fd로 반환된다. 이후 `read(fd)`, `write(fd)`, `close(fd)`는 이 fd를 통해 같은 file/device를 참조한다.

기본 fd:

- `0`: stdin
- `1`: stdout
- `2`: stderr

## Device File

Linux에서는 hardware도 파일처럼 다룬다. `/dev/zynq_sevenseg`는 application과 device driver 사이의 entry point다. 실제 driver 선택은 device file의 major number를 통해 이루어진다.

## `open()`

`open()`은 device file과 application 사이의 연결을 만든다.

Driver 관점에서는:

- device 사용 준비
- 필요한 초기화 수행 가능
- 성공 시 0 반환

Kernel은 driver의 `open` 함수를 호출하기 전후로 권한 확인, fd 관리, file object 생성 등 기본 처리를 수행한다.

## `write()`

`write()`는 user space data를 device로 보낸다. Kernel은 user memory를 직접 신뢰하거나 임의 접근하지 않으므로 `copy_from_user()`를 통해 user buffer를 kernel buffer로 복사한다. 이후 driver는 mapped virtual address에 값을 써서 hardware register를 제어한다.

## `read()`

`read()`는 반대 방향이다. Driver가 hardware register 또는 device memory에서 값을 읽어 kernel buffer에 저장하고, `copy_to_user()`로 user buffer에 전달한다.

## `close()`

`close()`는 fd를 닫고 device와 application 사이의 연결을 해제한다. Device 종류에 따라 close 시 hardware 상태를 초기화하거나 resource를 반환할 수 있다.

## PS와 PL의 관계

Zynq PS는 Linux kernel과 user application을 실행하는 CPU다. PL에는 sevenseg 같은 사용자 정의 hardware가 있고, PS와 PL은 AXI interconnect를 통해 memory mapped 방식으로 연결된다.

## 정리

14주차 예비의 핵심은 Linux 위에서 hardware를 제어할 때도 결국 핵심은 address mapping과 system call 흐름이라는 점이다. Application은 `/dev` 파일만 알고, driver가 kernel 내부에서 hardware 접근의 세부사항을 책임진다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **14주차 예비 - Linux Device Driver와 Device Control**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
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
- **14주차 예비 - Linux Device Driver와 Device Control**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [13주차 결과 - U-Boot Kernel Device Tree와 Driver 개념](13-result-u-boot-kernel-device-tree-driver.md) · 다음: [14주차 결과 - Sevenseg Driver 8-Byte Read Write](14-result-sevenseg-driver-8-byte-read-write.md)
