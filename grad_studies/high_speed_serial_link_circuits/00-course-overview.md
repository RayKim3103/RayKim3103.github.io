---
layout: page
title: "00. 강의 개요"
permalink: /grad_studies/high_speed_serial_link_circuits/00-course-overview/
sitemap: false
---

- **강의**: SYS6300, Special Topics in IC — Course Introduction (2026 Spring, Kwanseo Park, Yonsei University)

{% raw %}
## 개요

이 강의는 칩과 칩 사이(또는 보드와 보드 사이)에서 데이터를 초고속으로 주고받는 회로 — **고속 직렬 링크(High-Speed Serial Link)**, 흔히 **SerDes**(Serializer/Deserializer), **High-Speed I/O**, **Wireline Communication**이라 불리는 분야를 다룬다. PCIe, USB, Ethernet, HBM/DDR 같은 메모리 인터페이스, 디스플레이 인터페이스, 그리고 칩렛(Chiplet)/UCIe까지 — 오늘날 거의 모든 고성능 컴퓨팅 시스템의 "혈관"에 해당하는 회로를 설계 관점에서 학습한다.

## 강의 정보

| 항목 | 내용 |
|---|---|
| 과목 | SYS6300, Special Topics in IC, 3학점 |
| 강의 시간 | 수요일 13:00~15:50 |
| 강의자 | 박관서 (Kwanseo Park), Dept. of System Semiconductor Engineering |
| 강의 자료 | LearnUS에 매 강의 전 슬라이드 업로드 |

## 평가 방식

| 항목 | 비중 | 세부 |
|---|---|---|
| 출석 | 20% | 전자출결 |
| 시험 | 40% | **오픈소스(오픈북) 시험** — 강의자료 참고 가능 |
| 발표 | 40% | 20분 발표, IC 설계 분야 자유 주제 |

## 학기 진행 (Schedule)

### 전반부: 이론 강의

| Week | 날짜 | 내용 |
|---|---|---|
| 1 | 3/4 | Introduction |
| 2 | 3/11 | High-Speed Serial Link Overview |
| 3 | 3/18 | Channels, Transmission Lines |
| 4 | 3/25 | Transmitter |
| 5 | 4/1 | Receiver |
| 6 | 4/8 | EQ Basics & TX EQ |
| 7 | 4/15 | RX EQ |
| 8 | 4/22 | CDR Basics |

### 후반부: 시험·세미나·발표

| Week | 날짜 | 내용 |
|---|---|---|
| 9 | 4/29 | Exam |
| 10 | 5/6 | Invited Talk (1) — Academia |
| 11 | 5/13 | Invited Talk (2) — Industry |
| 12 | 5/20 | Final Presentation (1) |
| 13 | 5/27 | Final Presentation (2) |
| 14 | 6/3 | (지방선거일, 휴강) |
| 15 | 6/10 | Final Presentation (3) |
| 16 | 6/17 | Final Presentation (4) |

> 실제 강의는 이 스케줄보다 한 주 정도 밀려서 진행되었다. 이 노트의 Chapter 1~7은 실제 강의 자료(Lecture 1~7) 기준으로, Chapter 8은 시험 주간 이후의 초청 세미나·학생 발표 내용을 종합해서 정리했다.

## 학습 목표

강의 목표를 강의자의 표현 그대로 옮기면 다음과 같다.

```text
Study high-speed serial links
 - Trends for high-performance computing
 - Applications
 - Bandwidth, energy efficiency, throughput
 - Architecture evolutions
 - Mixed-signal architecture vs. ADC-based architecture
```

즉 이 강의는 특정 회로 하나를 깊게 파는 것이 아니라, **"HPC(고성능 컴퓨팅) 시스템에서 왜 고속 인터커넥트가 병목이 되는가"**라는 시스템적 질문에서 출발해, 그 병목을 해결하는 회로(TX/RX/EQ/CDR)를 순서대로 설계해 나가는 흐름으로 구성된다.

## 왜 지금 고속 직렬 링크인가 — HPC 관점

컴퓨팅 시스템은 크게 세 축으로 구성된다.

| 구성 요소 | 목표 | 최근 트렌드 |
|---|---|---|
| **Processor** (연산) | Processing Speed ↑ | 더 많은 코어, 고클럭 |
| **Memory** (저장) | Density & Bandwidth ↑ | HBM3E, PIM(Processing-in-Memory) |
| **Interconnect** (통신) | Bandwidth ↑, Latency ↓ | 고속 SerDes, Chiplet, UCIe, CoWoS, Silicon Photonics |

프로세서·메모리 각각의 성능은 계속 발전하고 있지만, 이 둘을 "연결"하는 인터커넥트가 병목이 되지 않으려면 **특화된 고속 인터페이스**가 반드시 필요하다 — 이 강의가 다루는 대상이 바로 이 인터커넥트다.

### 주요 응용 분야 (2025~2026 기준)

| 분야 | 대표 표준 | 최근 속도 목표 | 특징 |
|---|---|---|---|
| PCIe | PCIe 6.0/7.0 | 64~128 GT/s (PAM4) | PC·서버 내부 연결 |
| USB | USB4 v2 | ~80 Gbps | 범용 외부 연결 |
| Ethernet | 800G/1.6T | 200 Gbps/lane 목표 | 데이터센터 스위치-서버 |
| Memory | HBM3E, DDR5, LPDDR5X | HBM 9.6~12.8 Gbps/pin | GPU 메모리 대역폭 |
| Display | HDMI 2.1, DP 2.1 | 80 Gbps+ | 8K, 고주사율 |
| Chiplet/UCIe | UCIe 1.1/2.0 | 32~64 GT/s | 패키지 내 초단거리 초고대역폭 |
| Optical (미래) | 400G/800G/1.6T | 200 Gbps/lane | 데이터센터 장거리 |

## 고속 링크 성능을 특징짓는 세 가지 지표

| 지표 | 단위 | 의미 | 트렌드 |
|---|---|---|---|
| **Data Rate** | Gbps/lane, GT/s | 레인당 속도 | ↑↑ (200 Gbps급 목표) |
| **Energy Efficiency** | pJ/bit | 1비트 전송당 소비 에너지 | ↓↓ |
| **Throughput** | Tb/s, PB/s | 전체 시스템 처리량 | AI 학습 수요로 폭발적 증가 |

## 이 강의의 전체 구조를 미리 보기

강의는 하나의 트랜시버가 완성되어 가는 순서를 그대로 따라간다.

```text
Ch.1 개관 → Ch.2 채널(Channel) → Ch.3 송신기(TX) → Ch.4 수신기(RX)
   → Ch.5 송신단 EQ(TX FFE) → Ch.6 수신단 EQ(CTLE/DFE) → Ch.7 CDR
   → Ch.8 최신 연구 동향(세미나·논문 리뷰)
```

```text
Serializer                                   Deserializer
   TX ─────────── TX ── Channel ── RX ───────────── RX
  Data                                                Data
                    TX Clk         RX Clk
   Clock                                           CDR
Distribution ── PLL ────── Channel ──────── PLL ── Ref clk
  Ref Clk        (optional)               (optional)
```

이 트랜시버 블록도(Serializer–TX–Channel–RX–Deserializer, 그리고 Clock Distribution–PLL–CDR로 이어지는 클록 경로)는 이후 모든 장(chapter)의 도입부에 반복해서 등장하며, "지금 배우는 회로가 전체 그림의 어디에 해당하는지"를 계속 확인시켜 준다.

## 참고 자료 (강의 전체 공통)

- D.-K. Jeong, *Topics in IC Design*, Seoul National Univ.
- J. Kim, *Advanced Digital Integrated Circuits*, Seoul National Univ.
- S. Palermo, *ECEN720: High-Speed Links Circuits and Systems*, Texas A&M Univ.
- J. Han, *High-Speed IO Interface Intensive Program*, Hanyang Univ.
- W. J. Dally and J. W. Poulton, *Digital Systems Engineering*, Cambridge University Press, 1998
{% endraw %}

---

다음: [01. 고속 직렬 링크 개관](01-high-speed-serial-link-overview.md)
