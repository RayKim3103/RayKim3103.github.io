---
layout: page
title: "13주차 결과 - U-Boot Kernel Device Tree와 Driver 개념"
permalink: /studies/systems/embedded-system/13-result-u-boot-kernel-device-tree-driver/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/13%EC%A3%BC%EC%B0%A8%20%EA%B2%B0%EA%B3%BC%20-%20U-Boot%20Kernel%20Device%20Tree%EC%99%80%20Driver%20%EA%B0%9C%EB%85%90.md)

{% raw %}
이전: [12주차 결과 - Zynq Ubuntu Root File System 구성](12-result-zynq-ubuntu-root-file-system.md)  
다음: [14주차 예비 - Linux Device Driver와 Device Control](14-prep-linux-device-driver-control.md)

## 핵심 요약

이번 실습에서는 Zynq에서 Linux kernel을 booting하기 위한 boot image, device tree, kernel image, rootfs의 관계를 정리하고, 이후 사용할 device driver 개념을 학습했다. 핵심은 U-Boot가 kernel image와 device tree를 memory에 올리고, kernel은 root file system을 mount한 뒤 device driver를 통해 hardware를 제어한다는 점이다.

## 부팅 실험 과정

1. Xilinx device tree source 다운로드
2. `PATH`, `ARCH=arm`, `CROSS_COMPILE=arm-linux-gnueabihf-` 환경 변수 설정
3. Vivado에서 sevenseg project 생성, Block Design과 bitstream 생성
4. SDK에서 Board Support Package를 통해 device tree 생성
5. `system-top.dts` 관련 파일을 Linux server의 U-Boot device tree 경로로 복사
6. U-Boot Makefile에 `system-top.dtb` build target 추가
7. Docker 환경에서 U-Boot compile
8. 생성된 U-Boot 파일을 `u-boot.elf`로 변경하여 SDK에서 `BOOT.bin` 생성
9. Linux kernel build로 `uImage` 생성
10. `uEnv.txt` 작성
11. SD 카드 boot partition에 `BOOT.bin`, `system-top.dtb`, `uImage`, `uEnv.txt` 복사
12. MobaXterm serial port로 Linux boot 확인

## Boot 구성 요소

| 요소 | 역할 |
|---|---|
| Bootloader | hardware 초기화, kernel과 device tree load |
| Kernel image | OS 핵심 코드, process/memory/network/hardware resource 관리 |
| Device tree | hardware address, size, interrupt 등 구조 정보 |
| Root file system | `/bin`, `/etc`, `/lib`, `/dev` 등 OS 사용자 공간 |

## Bootloader 단계

| 단계 | 위치 | 역할 | 실행 메모리 |
|---|---|---|---|
| BL0 | iROM | boot mode pin 확인, BL1 load | iROM |
| BL1 | 외부 저장장치 | DRAM 등 주변장치 초기화, BL2 load | iRAM |
| BL2 | 외부 저장장치 | U-Boot 등 main bootloader, kernel load | DRAM |

이번 실습에서 다루는 U-Boot는 BL2 성격의 bootloader다. 개발자가 설정을 수정할 수 있고, device tree와 kernel image를 memory에 올린 뒤 kernel 실행을 시작한다.

## Kernel Image 종류

| 종류 | 설명 |
|---|---|
| `Image` | 압축되지 않은 kernel image |
| `zImage` | 압축된 kernel image, 자체 압축 해제 routine 포함 |
| `uImage` | U-Boot가 사용하는 image, zImage에 64Byte header 추가 |

이번 실습은 U-Boot와 `uImage`를 사용한다.

## Device Tree

Device tree는 hardware 구성을 tree 형태로 표현한다. CPU, memory, GPIO, SPI, I2C, interrupt controller, 사용자 IP의 address와 interrupt 정보를 kernel에 알려준다.

중요한 이유:

- kernel source를 직접 수정하지 않고 hardware 구성을 변경할 수 있다.
- 같은 kernel binary를 여러 board 설정에 재사용할 수 있다.
- PL에 추가한 사용자 IP를 Linux가 인식하게 만들 수 있다.

`dts`는 source이고, `dtc` device tree compiler를 거쳐 `dtb` blob으로 변환된다.

## Device Driver 개념

Device driver는 application과 hardware 사이의 kernel module이다.

```text
Application
-> system call(open/read/write/close)
-> device file
-> kernel chrdev table
-> device driver file_operations
-> mapped hardware address
-> device
```

## Device File과 Major/Minor Number

Linux에서 hardware는 파일처럼 다룬다. 예를 들어 `/dev/zynq_sevenseg` 같은 device file을 만들고 application이 `open`, `write`, `read`, `close`로 접근한다.

- Major number: kernel의 `chrdev[]`에서 어떤 driver인지 구분
- Minor number: 같은 driver 안에서 개별 device 구분
- Character device: byte stream처럼 순차 접근하는 device

## Driver 등록과 제거

| 명령/함수 | 역할 |
|---|---|
| `insmod sevenseg_driver.ko` | kernel module 삽입 |
| `mknod /dev/zynq_sevenseg c 222 0` | character device file 생성 |
| `open()` | device file 열기 |
| `write()` | user data를 device로 전달 |
| `read()` | device data를 user로 전달 |
| `close()` | device file 닫기 |
| `rmmod` | kernel module 제거 |
| `copy_from_user` | user space에서 kernel buffer로 복사 |
| `copy_to_user` | kernel buffer에서 user space로 복사 |
| `ioremap` | physical address를 kernel virtual address로 mapping |
| `iounmap` | address mapping 해제 |

## Kernel Image 크기 관계로 구조 확인

`uImage`가 `zImage`에 64byte header를 더한 것이라는 정의를 그대로 식으로 쓰면

$$
\text{size}(\text{uImage}) = \text{size}(\text{zImage}) + 64\text{byte}
$$

이 64byte header에는 U-Boot가 image를 memory에 올릴 때 필요한 정보(entry point address, load address, image 종류, checksum 등)가 들어간다 — U-Boot는 `Image`(비압축본)를 직접 다루지 않고, 압축된 `zImage`에 자기 인식용 header만 얹은 `uImage`를 표준 형식으로 사용한다. 이는 12주차의 FAT32/ext4 역할 분리와 같은 맥락으로, "부트로더가 필요한 최소 정보만 별도로 포장한다"는 임베디드 부팅의 공통 패턴이다.

## 고찰

동일한 boot file을 사용해도 SD 카드에 따라 serial port 연결이나 boot 과정에서 오류가 발생할 수 있었다. 가능한 원인은 SD 카드 호환성, 보드 노후화, 파일 read 오류, UART driver 환경 차이 등으로 추정된다.

## 복습 질문

- `uImage = zImage + 64byte header`라는 관계에서, header에는 어떤 정보가 담겨야 U-Boot가 이를 올바르게 load할 수 있는지 설명할 수 있는가?
- BL0/BL1/BL2 3단계 중 U-Boot가 왜 BL2에 해당하는지, 각 단계가 실행되는 메모리(iROM/iRAM/DRAM)와 함께 설명할 수 있는가?
- Device tree가 kernel source를 수정하지 않고도 hardware 구성 변경에 대응할 수 있게 해주는 이유를 설명할 수 있는가?
- 14주차에서 다룰 `/dev/zynq_sevenseg` device file이 이번 주차의 "chrdev table + major/minor number" 개념과 어떻게 연결되는지 설명할 수 있는가? ([14주차 결과](14-result-sevenseg-driver-8-byte-read-write.md) 참고)

## 정리

13주차 결과의 핵심은 embedded Linux가 boot되기 위해 bootloader, kernel, device tree, rootfs가 함께 필요하며, hardware 제어는 device file과 device driver를 통해 Linux 방식으로 추상화된다는 점이다.

{% endraw %}

---

이전: [12주차 결과 - Zynq Ubuntu Root File System 구성](12-result-zynq-ubuntu-root-file-system.md) · 다음: [14주차 예비 - Linux Device Driver와 Device Control](14-prep-linux-device-driver-control.md)
