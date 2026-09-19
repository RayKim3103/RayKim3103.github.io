---
layout: page
title: "01. 고속 직렬 링크 개관"
permalink: /grad_studies/high_speed_serial_link_circuits/01-high-speed-serial-link-overview/
sitemap: false
---

- **강의**: Lecture 1 — High-Speed Serial Link Overview

{% raw %}
## 개요

이 장은 본격적인 회로 설계에 들어가기 전, (1) 고속 직렬 링크가 무엇이고 왜 "직렬(serial)"이어야 하는지, (2) 신호 품질을 표현하는 기본 어휘(지터·채널 손실·아이 다이어그램·BER)를 정리하고, (3) 링크 속도가 올라가면서 트랜시버 아키텍처가 어떻게 단순한 플립플롭에서 오늘날의 복잡한 구조로 진화했는지를 훑는다.

## 왜 "직렬(Serial)" 링크인가

```text
Core/Memory/GPU ↔ Serializer(N:1) → [고속 1레인 Channel] → Deserializer(1:N) ↔ Core/Memory/GPU
       (저속, 병렬)                                                              (다시 저속, 병렬)
```

칩 내부는 저속·병렬(수백~수천 비트 폭)로 데이터를 다루지만, 칩과 칩 사이는 핀 수가 제한적이기 때문에 **소수의 레인을 극한까지 고속화**해서 직렬로 보내는 편이 효율적이다. Serializer(Ser)가 N:1로 병렬 데이터를 하나의 고속 스트림으로 묶고, 채널을 통과한 뒤 Deserializer(Des)가 1:N으로 다시 풀어낸다. 이 회로 전체를 **SerDes**라 부른다.

## 신호 무결성(Signal Integrity)의 기본 어휘

### 지터 (Jitter)

> SONET 규격의 정의: "이상적인 위치로부터 디지털 신호의 유의미한 순간(전이 시점 등)이 단기적으로 흔들리는 정도"

지터는 **Random Jitter(RJ)**와 **Deterministic Jitter(DJ)**로 나뉜다. 클록 지터는 Period Jitter, Cycle-to-Cycle Jitter, TIE(Time Interval Error) 등으로 세분화해 측정한다.

### 채널 손실 (Channel Loss)

전기 채널은 기본적으로 **저역통과(low-pass) 특성**을 가진다 — 주파수가 높아질수록 감쇠(attenuation)와 왜곡(distortion)이 커진다. (물리적 원인은 Ch.2에서 상세히 다룬다.)

### 아이 다이어그램 (Eye Diagram)

수신 신호를 심볼 주기마다 겹쳐 그리면 눈(eye) 모양이 나타난다. 이 "눈"이 얼마나 넓게 열려 있는지가 신호 품질을 직관적으로 보여준다 — **수직 마진(voltage margin)**과 **수평 마진(timing margin)** 두 축으로 평가한다.

### 비트 오류율 (Bit Error Rate, BER)

$$
BER = \frac{\text{잘못 수신된 비트 수}}{\text{전체 전송 비트 수}}
$$

예를 들어 $BER=10^{-12}$ 조건에서 지터가 약 1675ps, 아이 폭 마진이 약 200ps 정도 확보되어야 하며, 실제 시스템은 지터에 의해 제한되기 전까지 $BER=10^{-18}$ 수준까지도 잠재적으로 달성 가능하다는 것이 일반적인 설계 목표 범위다.

## 고속 직렬 링크의 트렌드: 대역폭·에너지·처리량

### 대역폭(Bandwidth) 수요

- 레인당 데이터 레이트는 **약 4년마다 거의 2배**씩 증가해왔다 (2000년대 초 수백 Mbps 수준 → 현재 200+ Gbps 목표).
- 이 증가는 **회로 기술의 발전**과 **공정 미세화(technology scaling)**가 함께 기여한 결과다.
- **PAM4 변조**가 스펙트럼 효율을 높여주기 때문에 많은 응용이 PAM4(또는 PAM3)를 채택하는 추세다 — 예: PCIe 6.0(64 Gb/s PAM4), USB4(80 Gb/s PAM3), GDDR7(36 Gb/s PAM3).

### 에너지 효율(Energy Efficiency) 수요

에너지 효율(1비트 전송에 드는 에너지, pJ/bit)은 **데이터 레이트와 채널 손실에 강하게 연동**된다. 채널이 길어질수록(예: DisplayPort 케이블 3ft → 30ft) 고주파에서 손실이 지수적으로 커지므로, **reach(도달 거리)를 늘리려면 전력을 더 써야 하는 트레이드오프**가 항상 존재한다.

### 처리량(Throughput) 수요 — AI가 만드는 새로운 압박

AI 모델의 파라미터 수는 2년마다 약 410배씩 증가하는 반면, 하드웨어 규모(스케일)는 2년마다 약 3배 증가하는 데 그친다 — 이 격차가 **"AI와 메모리 벽(AI and Memory Wall)"** 문제를 만든다. 이를 메우기 위해 NVIDIA Blackwell 플랫폼처럼 GPU를 **Scale-Up(한 패키지/보드 내 초고대역폭 연결, 예: GB200 Superchip)**과 **Scale-Out(랙/클러스터 단위 연결, 예: GB200 NVL72)**으로 동시에 확장하는 접근이 표준이 되고 있다.

지난 20년간의 스케일링 추이를 비교하면 그 불균형이 뚜렷하다.

| 항목 | 20년간 증가율 | 연간 환산 |
|---|---|---|
| HW FLOPS | 60,000× | ~3.0×/2년 |
| DRAM 대역폭 | 100× | ~1.6×/2년 |
| Interconnect 대역폭 | 30× | ~1.4×/2년 |

즉 **연산 성능(FLOPS)의 성장 속도가 메모리·인터커넥트 대역폭의 성장 속도를 압도적으로 앞지르고 있다** — 이것이 바로 이 강의가 다루는 고속 인터커넥트 회로 연구가 계속 중요해지는 근본적인 이유다.

## 아키텍처 진화: 왜 트랜시버가 점점 복잡해지는가

강의는 "가장 단순한 형태"에서 출발해, 속도가 올라갈 때마다 새로운 문제가 생기고 그 문제를 해결하는 회로가 하나씩 추가되는 과정을 보여준다.

### 1단계 — 순수 디지털 시스템

```text
TX: D-Q Flip-Flop → Channel(Data) → RX: D-Q Flip-Flop
```

가장 단순한 형태는 TX/RX를 그냥 플립플롭으로 구현하고, 별도의 클록 채널로 클록도 함께 보내는 것이다. **한계**: 대량의 데이터를 전송해야 하는 현실적 요구를 감당할 수 없다.

### 2단계 — Serializer/Deserializer + PLL 추가

```text
TX: N:1 Serializer → Channel(Data)
Ref Clk → PLL → Clock Distribution → Channel(Clock)
```

Ser/Des 회로가 코어 속도와 인터페이스 속도를 분리(decouple)해준다. PLL, 클록 분배, 분주기(divider) 같은 클로킹 회로가 새로 필요해진다.

### 3단계 — Termination 추가 (반사 문제)

고주파에서는 채널이 단순한 전선이 아니라 **전송선로(transmission line)**로 동작한다 → **반사(reflection)** 문제가 발생한다. 이를 해결하려면 임피던스 매칭을 위한 **종단 저항(termination resistor)**이 필요하다 ($Z_0 = Z_L$일 때 반사가 없다). Small-swing 신호를 위한 Driver, Buffer, Sampler도 함께 추가된다.

### 4단계 — De-skewing 회로 추가

데이터 경로와 클록 경로 사이의 **타이밍 스큐(timing skew)**가 샘플링 마진을 줄이고 데이터 레이트를 제한한다 → 위상 검출 로직과 de-skew 회로(위상 보정)가 추가된다.

### 5단계 — 이퀄라이저 추가 (ISI 문제)

**Intersymbol Interference (ISI)**: 이전 심볼이 완전히 정착(settle)되기 전에 다음 심볼이 오면 서로 간섭한다 — data-dependent jitter의 주 원인이다. ISI가 클수록 아이 히스토그램이 갈라진다. 이를 보상하기 위해 **FFE(TX), CTLE+DFE(RX)** 같은 이퀄라이저 회로가 추가된다.

```text
최종 형태:
TX: Driver+FFE → Channel(Data) → CTLE → DFE → Sampler → RX
Clock: PLL → Distribution → Channel(Clock) → Deskew/CDR → RX Ref Clk
```

이 5단계 진화 과정이 이 강의 Ch.2~7의 전체 목차와 정확히 대응된다 — **Ch.2(채널/반사) → Ch.3(TX/종단) → Ch.4(RX/샘플러) → Ch.5~6(이퀄라이저) → Ch.7(CDR/위상정렬)**.

## 두 가지 대표 트랜시버 아키텍처

### Mixed-Signal 아키텍처 (전통적)

```text
Data Path : Driver+FFE(TX) → Channel → CTLE → DFE → Sampler(RX)
Clock Path: PLL(TX) → Channel(옵션) → CDR → PLL(RX, 옵션)
```

- **장점**: 높은 에너지 효율, 낮은 지연시간(latency)
- **단점**: 복잡한 아날로그 회로, 낮은 확장성(scalability), 제한적인 이퀄라이제이션 능력
- 주 용도: ~100 Gbps 이하, 채널 손실 ~20~25dB

### ADC 기반 아키텍처 (최근 100+ Gbps 트렌드)

```text
Front-end: Serializer+DAC(TX) → Channel → VGA+CTLE → ADC(RX) → DSP(FFE/DFE/CDR/Calibration)
```

- **핵심**: 아날로그 신호를 ADC로 곧바로 디지털화한 뒤, 이퀄라이제이션을 **디지털 신호 처리(DSP)**로 수행
- **장점**: 공정 스케일링의 이점을 최대한 활용, 더 높은 채널 손실(30dB 이상)까지 보상 가능한 강력한 이퀄라이제이션
- **단점**: 고속·고해상도 ADC의 큰 전력 소모, 긴 CDR 지연시간(latency)
- 100+ Gbps 급 SerDes에서 지배적인 구조로 자리잡고 있다 (예: Marvell ISSCC'22, Huawei ISSCC'21 — 채널 손실 30~50dB 이상을 보상하는 장거리(long-reach) 트랜시버)

> ADC 기반 아키텍처는 Ch.8(최신 연구 동향)의 DSP 기반 통신 세미나 내용과 직접 연결된다.

## 시험·복습 체크포인트

- 왜 칩 간 통신이 "병렬"이 아니라 "직렬 + 고속화" 방향으로 발전했는가?
- Jitter, Channel Loss, Eye Diagram, BER이 각각 무엇을 측정하는 지표인지 설명할 수 있는가?
- 트랜시버 아키텍처가 5단계로 진화하는 과정에서, 각 단계마다 "무슨 문제"가 "무슨 회로"로 해결되었는가?
- Mixed-signal 아키텍처와 ADC 기반 아키텍처의 트레이드오프(전력 vs 이퀄라이제이션 능력, latency)는?
- AI 시대의 "Memory Wall" 문제에서 FLOPS, DRAM 대역폭, Interconnect 대역폭의 성장 속도 차이가 왜 문제가 되는가?
{% endraw %}

---

이전: [00. 강의 개요](00-course-overview.md) · 다음: [02. 채널과 전송선로](02-channels-and-transmission-lines.md)
