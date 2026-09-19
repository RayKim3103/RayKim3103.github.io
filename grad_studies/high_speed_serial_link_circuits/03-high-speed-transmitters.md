---
layout: page
title: "03. 고속 송신기 (Transmitter) 설계"
permalink: /grad_studies/high_speed_serial_link_circuits/03-high-speed-transmitters/
sitemap: false
---

- **강의**: Lecture 3 — High-Speed Transmitters

{% raw %}
## 개요

이 장은 트랜시버의 첫 관문인 **송신기(Transmitter, TX)**를 다룬다. 왜 단순한 CMOS 인버터가 고속 채널을 구동할 수 없는지에서 출발해, 종단(termination)·시그널링 방식(single-ended vs differential)·드라이버 토폴로지(voltage-mode vs current-mode)를 거쳐, PAM4/High-swing 드라이버 최신 연구까지 이어진다.

## TX에 필요한 것: 왜 인버터로는 안 되는가

CMOS 인버터의 출력 저항은 $V_{out}$이 0~$V_{DD}$로 스윙하는 동안 크게 변한다 — 이를 채널의 특성 임피던스 $Z_0$에 매칭시키기가 사실상 불가능해, 양단에서 반사(reflection)를 일으킨다. **→ 첫 번째로 필요한 것은 종단 저항(termination resistor)이다.**

## Termination (Ch.2 반사 이론의 TX 적용)

- **Double Termination**: RX 종단은 순방향 진행파의 에너지를 흡수하고, TX 종단은 임피던스 불연속에서 되돌아온 반사파의 에너지를 흡수한다 — SI는 최고지만 신호 스윙이 1/2로 감소한다.
- **Off-chip vs On-chip Termination**: Off-chip 종단은 패키지 기생성분으로 인한 unterminated stub 때문에 큰 반사를 유발한다. On-chip 종단은 정확한 저항값 구현이 어려워 PVT 변화에 대한 보정(calibration)이 필수다.

### Series vs Parallel Termination

| 방식 | 설명 |
|---|---|
| **Series Termination** | 저임피던스 **Voltage-mode** 드라이버가 주로 사용 |
| **Parallel Termination** | 고임피던스 **Current-mode** 드라이버가 주로 사용 |

### AC vs DC Coupling

| 방식 | 특징 |
|---|---|
| **DC Coupling** | AC/DC 성분 모두 통과. TX/RX의 common-mode 레벨이 결합됨 → 임의의(uncoded) 랜덤 데이터 허용 |
| **AC Coupling** | DC 성분 차단. TX/RX common-mode 레벨 분리. 1/0 불균형이 DC 레벨 드리프트를 유발 → 8b/10b, 64b/66b 같은 balancing code 필요 |

### Passive vs Active Termination

- **Passive (Poly 저항)**: 선형성과 정밀도가 좋지만, 공정·온도에 따라 ±30% 변동 → **트리밍(trimming)** 필요.
- **Active (트랜지스터)**: Triode 영역으로 바이어스된 FET는 작은 스윙(<500mV)에서 선형적으로 동작. 소형화와 디지털 트리밍이 가능하다는 장점.
- **On-Die Termination Calibration**: Off-chip 정밀 기준 저항과 비교해 On-chip 저항을 LSB 이내로 맞을 때까지 조정. 멀티채널 시스템에서는 채널별(per-channel) 캘리브레이션이 필요할 수 있다.

## Signaling: Single-Ended vs Differential

| 항목 | Single-Ended | Differential |
|---|---|---|
| 핀/와이어 수 | 비트당 1개 (핀 효율 100%) | 비트당 2개 (핀 효율 50%) |
| 노이즈 내성 | 취약 (common-mode 노이즈 그대로 유입) | 우수 (common-mode 노이즈가 상쇄됨) |
| 스윙 | $V_{swing}$ | $2\times V_{swing}$ (차분 시) |
| 기준 전압 | 필요 (정확도 중요) | 불필요 (zero-crossing이 기준) |
| 전력 | 높음 (풀 스윙 필요) | 낮음 (SNR 여유로 작은 스윙 가능) |
| 주 용도 | DRAM I/O 등 | PCIe, USB, Ethernet 등 대부분 SerDes |

Differential 신호는 신호 자신이 스스로의 기준(reference)이 된다는 점에서 근본적으로 유리하지만, 핀 수가 2배 필요하다는 비용이 있다 — 이 트레이드오프 때문에 최근 Chiplet/Die-to-Die(초단거리) 환경에서는 면적 효율이 좋은 Single-Ended가 다시 주목받고 있다 (Ch.8 Chord/Pseudo-differential signaling 연구와 연결).

## Current-Mode vs Voltage-Mode Driver

| 항목 | Current-Mode (CM) | Voltage-Mode (VM) |
|---|---|---|
| 종단 방식 | Shunt(병렬) termination, "전류"를 구동 | Series termination, "전압"을 구동 |
| 임피던스 제어 | 우수 (포화 트랜지스터로 높은 출력 저항) | 어려움 (MOSFET 동작 영역에 따라 변동) |
| 전력 | 높음 (항상 tail current 소비) | 낮음 (rail-to-rail 가능) |
| 대역폭/속도 | 높음 (differential pair + 작은 스윙) | Moderate (커패시턴스 부하 영향) |
| 주 용도 | PCIe/USB/Ethernet 등 (>10Gbps) | DDR, 일부 저전력 I/O |

### 동일 스윙에서 필요한 전류 비교

같은 $V_{D,PP}$(차분 출력 스윙)를 기준으로 종단 방식별 필요 전류 레벨:

| Driver | RX 종단 | 필요 전류 | 정규화 |
|---|---|---|---|
| CM | Single-ended | $V_{d,pp}/Z_0$ | 1× |
| CM | Differential | $V_{d,pp}/Z_0$ | 1× |
| VM | Single-ended | $V_{d,pp}/2Z_0$ | 0.5× |
| VM | Differential | $V_{d,pp}/4Z_0$ | **0.25×** |

동일 공급 전압 조건에서 **이상적인 Voltage-Mode 드라이버 + Differential RX 종단** 조합은 이론적으로 최대 **4배의 드라이버 전력 절감**이 가능하다. 다만 실제 전력은 출력 임피던스 제어, 프리드라이버 전력, 이퀄라이제이션 구현 방식에도 좌우된다.

### Current-Mode Logic (CML) 드라이버

고성능 시리얼 링크에서 널리 채택된다 — 스윙과 pre-emphasis(FFE) 레벨 제어가 쉽고, 포화된 트랜지스터로 높은 출력 저항을 얻어 임피던스 매칭이 좋다. 단, 전류 소모가 크다.

### Unipolar vs Bipolar Signaling

| 방식 | 정의 |
|---|---|
| Unipolar | Pull-only. Logic 0 = 0, Logic 1 = +2x |
| Bipolar (Push-pull) | Logic 0 = −x, Logic 1 = +x. "0"을 RX 기준으로 사용 가능. 대개 전력 소모가 더 낮음 |

**Push-Pull CM Driver**는 동일 스윙 대비 더 적은 전류가 흐르고, 듀얼 전류원으로 우수한 PSRR을 얻지만 전압 헤드룸 이슈가 있다.

## Voltage-Mode 드라이버 구현: N-over-N vs P-over-N

| 구분 | N-over-N | P-over-N |
|---|---|---|
| 적합 스윙 | 저스윙 (< 400~500mVpp) | 고스윙 (>1V, USB/PCIe 등) |
| 임피던스 특성 | 출력 전압에 종속적 | 출력 전압과 분리(decoupled) → 매칭 우수 |

- **N-over-N VM Driver**: 저스윙 동작 시 pull-up/pull-down NMOS가 triode 영역에서 동작. 임피던스는 디바이스 크기와 overdrive 전압으로 결정 — regulator로 공급 전압을 조절해 overdrive를 제어한다. Pre-driver와 main-driver의 regulator가 서로 연동되어야 하며 ($R_{ON,N1}+R_{ON,N2}+Z_0=2Z_0$), 피드백 루프로 동시에 동작한다.
- **P-over-N VM Driver**: pull-up(PMOS)/pull-down(NMOS)의 임피던스 캘리브레이션을 독립적으로 수행할 수 있다는 장점.

### SST (Source-Series Termination) Driver

CMOS 소자에 **선형화 저항을 직렬로** 배치해 출력 임피던스를 (패시브 저항 + 트랜지스터 triode 저항)의 직렬 조합으로 설정한다. 공정 변화에 따라 임피던스가 변할 수 있어, **Segmented SST Driver**로 활성화된 세그먼트 수를 조절해 임피던스 매칭을 보정한다 — 다만 중복(redundant) 세그먼트만큼 출력단·프리드라이버 전력이 증가하는 대가가 있다.

## PAM4 드라이버

PAM4 드라이버는 MSB/LSB 두 개의 데이터 경로를 가진다.

| 구현 방식 | 원리 |
|---|---|
| **SST 기반** | 저항 분배기 구조 — MSB/LSB 경로 저항이 2배 차이 |
| **CML 기반** | MSB/LSB 경로의 tail 전류를 다르게 설정 |

### RLM (Ratio of Level Mismatch)

PAM4는 3개의 레벨 간격이 균일해야 정확한 판정이 가능하다.

$$
RLM = \frac{\min(\text{레벨 간격})}{V_{pp}/3}
$$

이상적인 PAM4는 $RLM=1$이며, 대부분의 논문은 $RLM \ge 0.9$를 실용적 목표로 삼는다.

## High-Swing Driver 최신 연구

56G+ PAM4 등 고손실 채널을 극복하려면 $\ge 1V_{ppd}$의 높은 스윙이 필요하지만, 공급 전압 스케일링은 선형성 있는 고스윙 구현을 어렵게 만든다.

| 연구 | 핵심 기법 | 성과 |
|---|---|---|
| Swing-Boosted CML (Upadhyaya, VLSI'16) | 1.8V 전원에서 병렬 thick-oxide bleeder 전류원으로 출력 common mode 상승 | >1.2Vppd 스윙 |
| Tail-Less Current-Mode Driver (Steffan, ISSCC'17) | Replica-bias 네트워크로 캐스코드 게이트 전압 설정, thin-oxide 소자 사용 가능 | 1.2Vppd, RLM 94% |
| PAM4 Hybrid Voltage-Mode Driver (Bassi, JSSC'16) | MSB/LSB 구동 병렬 push-pull 전류원 | 1.3Vppd @ 28nm, RLM >94% |

## 실무 종합: 드라이버 선택 가이드 (강의 노트 기반)

| 데이터 레이트 | 권장 구성 |
|---|---|
| 25~56Gbps NRZ | Current-Mode CML + Double Termination + FFE |
| 56G+ PAM4 | High-Swing CML (Tailless/Bleeder/Hybrid) + 강력한 선형성 캘리브레이션 |
| Chiplet/Die-to-Die | 저스윙 Single-Ended Voltage-Mode + 최소 이퀄라이저 |

**핵심 트레이드오프 요약**: Impedance Control은 Current-Mode가 우세하고, Power Efficiency는 Voltage-Mode가 우세하다. 이 때문에 고속(>25Gbps)·장거리 링크는 Current-Mode(CML)가 주류이고, 저전력·단거리·Chiplet 응용에서는 Voltage-Mode(혹은 하이브리드)가 재부상하고 있다.

## 시험·복습 체크포인트

- CMOS 인버터를 그대로 TX 드라이버로 쓸 수 없는 이유는?
- Current-Mode와 Voltage-Mode 드라이버가 각각 어떤 종단 방식을 쓰는지, 그리고 동일 스윙 대비 필요 전류가 왜 다른지 설명할 수 있는가?
- N-over-N과 P-over-N VM 드라이버의 적용 스윙 범위와 임피던스 특성 차이는?
- Segmented SST Driver가 임피던스 캘리브레이션을 수행하는 원리와 그 대가는?
- RLM이 무엇을 측정하며, 왜 PAM4 드라이버에서 특히 중요한가?
- High-swing driver 연구들(Bleeder, Tailless, Hybrid)이 공통으로 추구하는 목표는 무엇인가?
{% endraw %}

---

이전: [02. 채널과 전송선로](02-channels-and-transmission-lines.md) · 다음: [04. 고속 수신기 설계](04-high-speed-receivers.md)
