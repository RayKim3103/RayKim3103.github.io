---
layout: page
title: "02. Storage & I/O — 디스크 · 버스 · 인터럽트 · DMA"
permalink: /studies/arch/microprocessor/02-storage-and-io/
sitemap: false
---

- **원본**: [GitHub — Micro Processor](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Micro_Processor) · `04 Computer Organization Ch6 — Storage and I/O Topics` + `04A Annotated Copy` **통합**
- `04A`는 강의 노트에 "84쪽 같은 강의의 별도 사본, 텍스트 추출 기준 본문 내용 동일"이라고 명시된 **annotated copy**라서 하나로 합쳤습니다.
- 교재: *Computer Organization and Design* (Patterson & Hennessy) Ch6 계열.

{% raw %}
## 개요

Processor만 빨라져도 전체 system 성능은 **I/O에서 병목**이 된다. 이 장은 I/O·storage의 성능 지표(latency vs throughput vs bandwidth), hard disk access time, bus 구조와 protocol, arbitration, OS의 I/O 관리, 그리고 **polling / interrupt / DMA**라는 CPU–device 상호작용 세 방식을 다룬다.

---

## 1. I/O의 중요성

Computer의 다섯 구성 요소: **processor / memory / input / output / datapath·control**.

Amdahl 관점 — processor를 아무리 빠르게 해도 I/O 시간이 그대로면 speedup은 그 부분에 막힌다.
예: 프로그램의 10%가 I/O이고 나머지 90%를 10배 빠르게 → 전체 시간 = 0.1 + 0.09 = 0.19 → **약 5.3배** (10배가 아님).

I/O device는 세 축에서 다양하다.

| 축 | 예 |
|---|---|
| **behavior** | input(읽기 전용) / output(쓰기 전용) / storage(양방향) |
| **partner** | human(keyboard, mouse, display) / machine(disk, network) |
| **data rate** | keyboard 수 B/s ~ network·disk 수백 MB/s ~ GB/s |

---

## 2. Latency · Throughput · Bandwidth

| 지표 | 의미 | 중요한 상황 |
|---|---|---|
| **Latency (response time)** | 한 요청이 완료되기까지 시간 | 사용자 상호작용, random access |
| **Throughput (bandwidth)** | 단위 시간당 완료되는 작업량 | 대용량 전송, batch I/O |
| **Bandwidth** | link가 물리적으로 전달 가능한 최대 data rate | bus / network / storage 매체 |

- **Block size ↑** → sequential throughput ↑ 이지만 **작은 요청의 latency ↑**. 두 지표는 자주 상충한다.
- 병렬 처리(여러 disk, 여러 NAND channel)로 throughput을 올려도 개별 요청 latency는 그대로일 수 있다.

---

## 3. Hard Disk Access Time

```text
Disk service time = seek time + rotational latency + transfer time + controller overhead
```

| 항목 | 의미 | 크기 감각 |
|---|---|---|
| **Seek time** | head가 목표 track으로 이동 | 수 ms (평균 seek는 최대 seek의 약 1/3) |
| **Rotational latency** | 목표 sector가 head 아래로 올 때까지 대기 | 평균 = 반 바퀴 |
| **Transfer time** | 실제 bit를 읽고/쓰는 시간 | track transfer rate에 의존 |
| **Controller overhead** | disk controller 처리 시간 | 수십 µs |

**평균 rotational latency**:
```text
Average rotational latency = 0.5 회전 / RPM
= 0.5 / (RPM / 60)  [초]
```
예: 5400 RPM → 0.5 / 90 ≈ 5.6 ms · 7200 RPM → ≈ 4.2 ms · 15000 RPM → ≈ 2.0 ms.

**계산 예**: 512 B sector, 평균 seek 5 ms, 10000 RPM, transfer rate 40 MB/s, controller overhead 0.1 ms
- rotational latency = 0.5 / (10000/60) ≈ 3.0 ms
- transfer = 512 / 40e6 ≈ 0.013 ms
- 합계 ≈ 5 + 3.0 + 0.013 + 0.1 ≈ **8.1 ms**

→ seek + rotational latency가 지배적. 그래서 random 4 KB access가 HDD의 약점이고, SSD가 이 부분에서 압도한다([04](04-ssd-basics-and-architecture.md)).

---

## 4. Network

Computer 사이 data communication. 성능은 **bandwidth + latency**로 평가하며, **hardware latency**(전송·전파 지연)와 **software latency**(protocol stack, OS overhead) 둘 다 영향을 준다.

- **Point-to-point**: 전용 link, contention 없음, 확장 시 link 수 증가.
- **Shared network**: 매체 공유, contention·arbitration 필요, 저비용.

---

## 5. Bus

**Bus** = 여러 component가 공유하는 communication link. 세 종류의 line:

| line | 역할 |
|---|---|
| **address lines** | 어느 위치/장치를 접근할지 |
| **data lines** | 실제 data |
| **control lines** | R/W, 요청·응답, clock, 우선순위 신호 |

Bus transaction = **address phase**(주소·명령 전송) + **data phase**(data 전송).

### Bus 종류

| Bus | 특징 |
|---|---|
| **Processor–memory bus** | CPU–memory 전용, 짧고 빠름, 특정 시스템에 최적화 |
| **I/O bus** | 여러 peripheral 연결, 길고 표준화가 중요(USB, PCIe 등) |
| **Backplane bus** | processor·memory·I/O가 한 bus에, 확장 slot 중심 |

- **Single-bus**: 단순·저비용 but 병목.
- **Two-bus / three-bus**: processor–memory traffic과 I/O traffic 분리 → 성능·확장성 ↑, bridge 필요.

### Synchronous vs Asynchronous

| | Synchronous bus | Asynchronous bus |
|---|---|---|
| 타이밍 기준 | 공통 clock | request/acknowledge **handshake** |
| 장점 | 단순, 빠름 | 느린·빠른 device 혼용 가능, bus 길이 자유 |
| 단점 | clock skew·bus 길이·최저속 device가 clock period 제한 | control 복잡, handshake overhead |

**Asynchronous read handshake 예**:
1. Master가 address + `ReadReq` 를 낸다.
2. Slave가 요청을 인식하고 `Ack` 를 올린다 (address latch).
3. Slave가 data를 준비해 data line에 올리고 `DataRdy` 를 올린다.
4. Master가 data를 읽고 `Ack` 를 내린다.
5. Slave가 `DataRdy` 를 내리며 transaction 종료.

### Bus 성능 향상 기법

data bus width ↑ · **block transfer**(burst) · **split transaction**(요청과 응답 사이 bus를 다른 transaction에 양보) · overlapped arbitration · **bus parking**(직전 master가 bus grant 유지).
→ 대가: complexity, arbitration overhead, latency 증가 가능.

### Multiplexed vs 분리

- **multiplexed bus**: address·data line을 시간적으로 공유 → 핀 수 ↓, 속도 ↓.
- **분리(dedicated)**: address·data 동시 전송 → 빠름, 핀 수 ↑.

---

## 6. Bus Arbitration

여러 **bus master**가 bus를 공유하려면 누가 쓸지 결정해야 한다.

| 방식 | 동작 | 장점 | 약점 |
|---|---|---|---|
| **Centralized parallel** | 중앙 arbiter가 각 master의 request line을 보고 grant | priority·fairness 관리 명확 | arbiter가 병목·single point of failure |
| **Daisy chain** | grant 신호가 장치를 순서대로 통과 | 배선 단순, 확장 쉬움 | 위치가 priority → **불공정**, 앞쪽 고장 시 뒤 전체 마비 |
| **Distributed self-selection** | 각 장치가 request를 보고 스스로 우선순위 판단 | 중앙 장치 불필요 | 구현 복잡 |
| **Collision detection** | 충돌을 감지하고 backoff 후 재시도 | shared medium(Ethernet류)에 적합 | 충돌 시 재시도 overhead |

Arbitration은 **priority ↔ fairness ↔ latency ↔ cost**의 tradeoff.

---

## 7. OS와 I/O Interface

I/O는 반드시 **OS를 통해** 관리된다. 이유:

- **protection**: user program이 남의 disk 영역·device를 직접 못 건드리게
- **resource sharing** / **scheduling**: 여러 프로세스의 I/O 요청 조정
- **device abstraction**: 다양한 device를 공통 인터페이스(파일, stream)로
- **error handling**: 재시도, 오류 보고

OS는 **device driver**를 통해: device register 접근, interrupt 처리, buffer 관리, DMA setup 을 수행한다.

---

## 8. CPU–Device 상호작용: Polling / Interrupt / DMA

| 방식 | CPU 역할 | 장점 | 단점 |
|---|---|---|---|
| **Polling** | status register를 주기적으로 읽음 | 단순, 예측 가능한 control flow, HW 추가 없음 | **CPU time 낭비**(busy wait), polling 간격 사이 event 놓침 |
| **Interrupt** | event 발생 시 device가 CPU를 interrupt → handler 실행 | CPU가 대기로 낭비 안 함, event-driven | interrupt overhead(문맥 저장/복원), priority·nesting 관리, 너무 잦으면 overhead 폭증 |
| **DMA** | 전송 파라미터만 setup, 나머지는 controller가 | **대량 전송**에 최적, CPU 개입 최소 | HW 비용, **cache coherence**·주소 변환·bus contention 문제 |

### Polling 세부
- 저속 device나 아주 단순한 시스템에 적합.
- 전송률이 높으면 polling 빈도를 그만큼 높여야 해서 CPU를 다 잡아먹는다.

### Interrupt 세부
- 흐름: device가 interrupt request → CPU가 현재 instruction 완료 → PC·상태 저장 → **interrupt vector**로 점프 → handler → 복귀.
- **priority / nested interrupt**: 더 급한 interrupt가 handler 실행 중 다시 발생하는 경우 처리.

### DMA 세부
절차:
1. CPU가 **source 주소, destination 주소, length, direction**을 DMA controller register에 씀.
2. DMA controller가 **bus master**가 되어 memory ↔ device 간 data를 직접 전송 (cycle stealing / burst mode).
3. 전송 완료 시 **interrupt**로 CPU에 알림.

DMA가 만드는 문제:
- **Cache coherence**: DMA가 memory를 갱신했는데 CPU cache에 옛 값이 남음 (또는 그 반대). → cache flush/invalidate, hardware coherence, non-cacheable 영역으로 해결.
- **가상주소 ↔ 물리주소**: DMA는 보통 물리주소로 동작 → OS가 buffer를 물리적으로 연속되게 잡거나 scatter-gather DMA 사용.
- **Bus contention**: DMA가 bus를 점유하는 동안 CPU의 memory 접근이 지연.

---

## 9. I/O Processor

DMA controller = **hard-wired special-purpose I/O processor**로 볼 수 있다.
더 일반적인 **I/O processor(channel processor)**는 자체 firmware/software를 실행하며 복잡한 I/O 작업(여러 전송의 sequencing, 형식 변환, 오류 복구)을 CPU 대신 처리한다. 대형 시스템에서 CPU를 I/O 관리 부담에서 해방시킨다.

---

## 복습 질문

- Disk service time의 네 항목과, 10000 RPM에서 평균 rotational latency는?
- Synchronous bus와 asynchronous bus의 타이밍 기준·장단점 차이는? asynchronous read handshake 순서는?
- Daisy chain arbitration이 "불공정"한 이유는? centralized parallel의 약점은?
- Polling / interrupt / DMA를 CPU 개입량과 적합한 상황으로 구분하면? DMA가 유발하는 세 가지 문제는?
{% endraw %}

---

이전: [01. ARM 아키텍처·어셈블리](01-arm-architecture-and-assembly.md) · 다음: [03. Multiprocessors](03-multiprocessors.md)
