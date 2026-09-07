---
layout: page
title: "12주차 결과 - Zynq Ubuntu Root File System 구성"
permalink: /studies/systems/embedded-system/12-result-zynq-ubuntu-root-file-system/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Embedded_System/lecture_notes/12%EC%A3%BC%EC%B0%A8%20%EA%B2%B0%EA%B3%BC%20-%20Zynq%20Ubuntu%20Root%20File%20System%20%EA%B5%AC%EC%84%B1.md)

{% raw %}
이전: [11주차 결과 - PL Interrupt와 PS Handler](11-result-pl-interrupt-ps-handler.md)  
다음: [13주차 결과 - U-Boot Kernel Device Tree와 Driver 개념](13-result-u-boot-kernel-device-tree-driver.md)

## 핵심 요약

이번 실습은 Zynq 보드에서 Linux를 부팅하기 위한 Ubuntu 기반 root file system을 구성하는 과정이다. 핵심은 ARM용 rootfs를 x86 Linux server에서 `qemu-user-static`과 `chroot`로 준비하고, SD 카드의 ext4 partition에 복사하여 보드가 사용할 사용자 공간 환경을 만드는 것이다.

## 목표

- Linux OS 부팅에 필요한 root file system을 만든다.
- ARM 32bit 환경을 x86 host에서 emulation하여 package를 설치한다.
- SD 카드 partition을 boot용 FAT32와 rootfs용 ext4로 구성한다.
- UART console login을 위한 설정을 준비한다.

## 실험 과정

1. MobaXterm으로 Linux server 접속
2. `u-boot-xlnx` 작업 폴더에서 `zynq_xenial_rootfs` 생성
3. ARM processor용 Ubuntu rootfs 다운로드
4. `qemu-user-static` 설치 및 rootfs 내부로 복사
5. `/proc` mount 및 DNS 설정용 `resolv.conf` 복사
6. `chroot`로 ARM rootfs 환경 진입
7. root password와 user 계정 설정
8. 필요한 package 설치
9. UART login과 mount 관련 설정 파일 수정

## `qemu-user-static`

x86 host에서 ARM binary를 실행하기 위한 emulation 도구다. ARM rootfs 내부에서 `apt-get`, shell, ARM binary 등을 실행하려면 필요하다.

## `/proc` mount

`/proc`은 kernel이 제공하는 virtual file system이다. process, CPU, memory 상태 같은 runtime 정보를 제공한다. `chroot` 내부는 별도 root처럼 보이므로 `/proc`을 mount하지 않으면 `ps`, `top`, `/proc/cpuinfo` 등 system 정보 접근이 제대로 되지 않는다.

## `resolv.conf`

DNS 설정 파일이다. `apt-get`, `wget` 등이 domain name을 IP 주소로 바꾸려면 nameserver 정보가 필요하다. `chroot` 환경에는 독립적인 네트워크 설정이 부족하므로 host의 `/etc/resolv.conf`를 복사해 외부 인터넷 접근을 가능하게 한다.

## 설치 package와 역할

| Package | 역할 |
|---|---|
| `sudo` | 일반 사용자에게 root 권한 명령 허용 |
| `vim` | 설정 파일 편집 |
| `make` | Makefile 기반 build |
| `gcc` | C compiler |
| `build-essential` | compile에 필요한 기본 도구 묶음 |
| `kmod` | kernel module load/unload |
| `libssl-dev` | SSL/TLS 개발 header |
| `libncurses5-dev` | terminal UI 기반 build 지원 |
| `bc` | kernel build 중 계산 도구 |
| `udev` | device node 자동 생성 |
| `dialog` | terminal UI script 지원 |
| `net-tools` | `ifconfig`, `netstat` 등 |
| `iproute2` | `ip addr`, `ip link` 등 modern network tool |

## UART Console 설정

| 파일 | 목적 |
|---|---|
| `/etc/init/ttyPS0.conf` | boot 후 `ttyPS0`에 login prompt 표시 |
| `/etc/securetty` | `ttyPS0`에서 root login 허용 |
| `/etc/fstab` | boot 시 filesystem 자동 mount |

`ttyPS0`는 Zynq PS의 UART console 장치로, MobaXterm serial port와 연결되어 보드 Linux shell에 접근할 수 있게 한다.

## SD 카드 partition 구성

Zynq에서 Ubuntu를 부팅하려면 SD 카드에 두 영역이 필요하다.

| Partition | Format | 내용 |
|---|---|---|
| 1번 | FAT32 | boot image, device tree, kernel image, uEnv |
| 2번 | ext4 | root file system |

작업 순서:

1. 기존 mount 해제
2. `fdisk`로 기존 partition 삭제
3. 1GB boot partition 생성
4. boot 가능 flag 설정
5. 남은 공간으로 rootfs partition 생성
6. boot partition을 FAT32로 format
7. rootfs partition을 ext4로 format
8. rootfs partition mount
9. `zynq_xenial_rootfs` 내용 복사
10. mount 해제

Windows는 ext4를 기본 인식하지 못하므로 rootfs partition 작업은 Linux 환경에서 수행해야 한다.

## 왜 `/proc`은 복사하지 않는가

`/proc`은 실제 파일 모음이 아니라 kernel이 runtime에 제공하는 virtual file system이다. rootfs를 SD 카드로 복사할 때 `/proc` 내용은 의미가 없고, 오히려 예기치 않은 오류를 만들 수 있으므로 복사하지 않는다.

## 정리

12주차 결과의 핵심은 embedded Linux boot를 위해 boot image뿐 아니라 제대로 구성된 root file system이 필요하다는 점이다. 이번 주차는 SD 카드의 root partition을 준비하고, 다음 주차에서 boot image와 kernel, device tree를 결합해 실제 Linux boot로 이어진다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **12주차 결과 - Zynq Ubuntu Root File System 구성**를 다루며, Zynq/FPGA 기반 임베디드 시스템에서 hardware IP, device tree, Linux driver, boot flow를 실습으로 연결한다.
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
- **12주차 결과 - Zynq Ubuntu Root File System 구성**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [11주차 결과 - PL Interrupt와 PS Handler](11-result-pl-interrupt-ps-handler.md) · 다음: [13주차 결과 - U-Boot Kernel Device Tree와 Driver 개념](13-result-u-boot-kernel-device-tree-driver.md)
