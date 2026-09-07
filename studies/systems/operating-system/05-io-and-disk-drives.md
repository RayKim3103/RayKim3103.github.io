---
layout: page
title: "05. IO와 디스크 드라이브"
permalink: /studies/systems/operating-system/05-io-and-disk-drives/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/05%20IO%EC%99%80%20%EB%94%94%EC%8A%A4%ED%81%AC%20%EB%93%9C%EB%9D%BC%EC%9D%B4%EB%B8%8C.md)

{% raw %}
tags: #operating-system #io #disk #dma #interrupt #raid

관련 노트: [운영체제 개요](01-os-overview.md), [파일 시스템](06-file-systems.md)

## 핵심 요약

I/O 장치는 CPU와 메모리보다 느리고, 장치마다 protocol이 다르다. 운영체제는 device interface, driver, interrupt, DMA, disk scheduling을 통해 장치의 복잡성을 숨기고 효율적인 I/O를 제공한다. 이 장은 canonical device model에서 시작해 HDD의 seek/rotation/transfer time, disk scheduling, RAID level 0/1/4/5까지 다룬다.

## 시스템 구조와 Interconnect

컴퓨터 시스템은 CPU, memory, peripheral device를 여러 bus와 interconnect로 연결한다. 빠른 장치와 느린 장치가 같은 방식으로 CPU에 붙어 있지 않으므로, OS는 hierarchy와 device 특성을 고려해야 한다.

고속 interconnect에는 GPU나 빠른 저장장치가 연결될 수 있고, 더 느린 peripheral bus에는 키보드, 마우스, 일반 I/O 장치가 연결될 수 있다.

## Canonical Device

I/O device는 크게 두 부분으로 볼 수 있다.

- Interface: OS가 읽고 쓰는 register와 protocol
- Internal structure: 장치 내부의 실제 동작 구현

Device interface에는 보통 status, command, data register가 있다.

- Status register: 장치가 준비되었는지, busy인지, error가 있는지 알려준다.
- Command register: 장치에 수행할 명령을 전달한다.
- Data register: 장치와 주고받는 데이터를 담는다.

## Polling 기반 Device Protocol

기본 protocol은 다음과 같다.

1. OS가 status register를 읽어 device ready 상태를 확인한다.
2. Data register에 데이터를 쓰거나 읽는다.
3. Command register에 명령을 쓴다.
4. 장치가 작업을 끝낼 때까지 반복 확인한다.

Polling은 단순하지만 장치가 오래 걸리는 동안 CPU가 계속 loop를 돌며 시간을 낭비한다.

## Interrupt

Interrupt는 장치가 작업 완료를 CPU에 알려 OS가 다른 일을 하다가 돌아올 수 있게 한다. 긴 latency의 I/O에서는 CPU 낭비를 줄일 수 있다.

하지만 interrupt가 항상 최선은 아니다.

- 매우 빠른 장치에서는 interrupt 처리 overhead가 polling보다 클 수 있다.
- 많은 interrupt가 몰리면 interrupt storm처럼 CPU가 interrupt 처리에 묶일 수 있다.
- High-performance I/O에서는 polling과 interrupt를 workload에 맞게 섞기도 한다.

## Programmed I/O와 DMA

Programmed I/O(PIO)는 CPU가 직접 memory와 device 사이 데이터를 옮긴다. 구현은 단순하지만 큰 data block에서는 CPU 시간이 낭비된다.

DMA(Direct Memory Access)는 DMA engine이 memory와 device 사이 데이터 이동을 맡는다. OS는 DMA에 memory address, size, direction을 알려주고, 장치는 직접 memory에 접근한다. CPU는 command 설정과 완료 interrupt 처리만 담당한다.

DMA의 핵심 장점은 CPU overhead를 줄이는 것이다. 단, cache coherence와 memory protection을 고려해야 한다.

## I/O Instruction과 Memory-Mapped I/O

OS가 장치와 통신하는 방법은 크게 두 가지다.

- Explicit I/O instruction: x86의 `in/out` 같은 별도 명령으로 I/O port에 접근한다.
- Memory-mapped I/O: device register를 물리 주소 공간 일부에 mapping하고 일반 load/store로 접근한다.

Memory-mapped I/O는 CPU의 일반 memory access mechanism을 활용할 수 있어 현대 시스템에서 널리 쓰인다.

## Device Driver

Device driver는 장치별 세부 protocol을 OS의 공통 interface에 맞춰 감싼다. 파일 시스템이나 네트워크 stack은 driver 덕분에 장치 내부 구현을 몰라도 된다.

Driver의 역할:

- Device register access
- Command submission
- Interrupt handling
- Error handling
- OS의 generic I/O layer와 device-specific logic 연결

## HDD 구조와 지연 요소

Hard disk drive는 platter, track, sector, disk head로 구성된다. 데이터를 읽으려면 head가 원하는 track으로 이동하고, platter가 회전해 원하는 sector가 head 아래로 와야 하며, 이후 data transfer가 진행된다.

Disk I/O time:

```text
Tio = Tseek + Trotation + Ttransfer
```

- Seek time: head가 목표 track으로 이동하는 시간
- Rotational delay: 목표 sector가 head 아래로 올 때까지 기다리는 시간
- Transfer time: 실제 data를 읽거나 쓰는 시간

Random access에서는 seek와 rotation이 큰 비중을 차지한다. Sequential access는 head 이동과 회전 대기가 줄어 훨씬 빠르다.

## Disk Scheduling

여러 disk request가 대기할 때 순서를 바꾸면 seek time과 rotational delay를 줄일 수 있다.

### SSTF

Shortest Seek Time First는 현재 head 위치에서 가장 가까운 track의 request를 먼저 처리한다. 평균 seek time을 줄일 수 있지만, 멀리 있는 request가 starvation될 수 있다.

### SPTF

Shortest Positioning Time First는 seek time뿐 아니라 rotational delay까지 포함한 positioning time이 가장 짧은 request를 고른다. 실제 disk 성능에는 rotation도 중요하므로 SSTF보다 정확한 기준이다.

## RAID

RAID는 여러 disk를 묶어 성능, 용량, 신뢰성을 개선하는 storage 기법이다.

### RAID 0: Striping

Data block을 여러 disk에 나누어 저장한다. Parallel access로 throughput이 좋아지고 전체 용량을 모두 사용할 수 있다. 그러나 redundancy가 없어 disk 하나가 고장나면 전체 data를 잃는다.

### RAID 1: Mirroring

같은 data를 두 disk에 복제한다. 하나가 고장나도 다른 disk로 복구할 수 있다. Read는 병렬화할 수 있지만 usable capacity는 절반 수준이다.

### RAID 4: Parity

Data disk들과 별도 parity disk를 둔다. 하나의 disk failure는 parity로 복구할 수 있다. Small write에서는 data와 parity를 함께 갱신해야 하며, parity disk가 bottleneck이 되기 쉽다.

### RAID 5: Rotating Parity

Parity block을 여러 disk에 분산한다. RAID 4의 parity disk bottleneck을 줄인다. 하나의 disk failure를 견딜 수 있고, capacity overhead는 disk 하나 분량이다.

## 복습 체크포인트

- Polling과 interrupt의 장단점을 latency와 overhead 관점에서 비교할 수 있는가?
- PIO와 DMA의 차이를 CPU 개입 정도로 설명할 수 있는가?
- Memory-mapped I/O가 무엇인지 설명할 수 있는가?
- HDD I/O time을 seek, rotational delay, transfer time으로 나누어 계산할 수 있는가?
- SSTF와 SPTF의 차이를 말할 수 있는가?
- RAID 0, 1, 4, 5의 성능과 신뢰성 trade-off를 비교할 수 있는가?

{% endraw %}

---

이전: [04. 멀티스레딩](04-multithreading.md) · 다음: [06. 파일 시스템](06-file-systems.md)
