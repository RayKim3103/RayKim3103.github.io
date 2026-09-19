---
layout: page
title: "07. 클록 데이터 복원 (Clock and Data Recovery)"
permalink: /grad_studies/high_speed_serial_link_circuits/07-clock-and-data-recovery/
sitemap: false
---

- **강의**: Lecture 7 — Clock and Data Recovery

{% raw %}
## 개요

지금까지 TX(Ch.3)·RX(Ch.4)·이퀄라이저(Ch.5~6)는 모두 "샘플링에 쓸 올바른 클록이 이미 있다"고 가정했다. 이 장은 그 클록을 **어떻게 만들어낼 것인가**를 다룬다 — TX 클록은 채널을 거치며 지연·지터·위상이 불확실해지므로, RX는 스스로 데이터로부터 최적의 클록을 복원(Clock and Data Recovery, CDR)해야 한다. Equalization과 함께 트랜시버의 "두 기둥" 중 하나이며, 강의자 본인의 연구 분야이기도 해 논문 인용이 특히 풍부하다.

## CDR의 세 가지 기능

```text
1. Phase Acquisition : 데이터 엣지 중심에 클록 rising edge를 맞춤 (샘플링 마진 최대화)
2. Phase Tracking     : 데이터 지터를 실시간으로 추적 (배경 보정)
3. Data Recovery      : 클록으로 0/1을 판정 (샘플러 포함)
```

## 클록킹 아키텍처 3분류

채널과 클록 소스의 관계에 따라 분류한다.

| 아키텍처 | 주파수 | 위상 관계 | 용도 |
|---|---|---|---|
| **Synchronous** | 동일 | 알려져 있고 고정 (동일 길이 라우팅) | 저속 버스 |
| **Mesochronous** | 동일 | 알 수 없음 (같은 소스 공유) | Forwarded clocking (메모리, 멀티레인) |
| **Plesiochronous** | 거의 동일(작은 $\Delta f$) | 독립적 소스 | 고속 SerDes (embedded clock) — **CDR 필요** |

### Synchronous Data Transmission

TX/RX가 동일 클록 도메인에 있다(액티브 deskew 없음) — 동일 길이 라우팅이 필요하고, 온칩 회로·보드 라우팅의 지연 변동에 민감하다. 최대 데이터 레이트가 채널 지연시간(latency)에 종속된다.

### Source Synchronous Link (Forwarded Clocking)

동일 주파수·미지 위상(mesochronous)의 전형적인 예 — 간단한 deskew 회로로 위상만 맞추면 된다. 전용 클록 채널이 필요하며, 멀티채널 시스템에서는 이 비용을 분산(amortize)할 수 있지만 레인 간 스큐가 문제가 되어 per-pin skew 보정이 필요하다. Forwarded clock RX는 PLL/DLL/PI(Phase Interpolator)/ILO(Injection-Locked Loop) 등 다양한 de-skewing 회로를 사용할 수 있으며, **최적 지터 추적 대역폭은 스큐 미스매치와 관련**이 있어 이를 고려해 de-skewing 방식을 선택해야 한다.

### Embedded Clocking

전용 클록 채널 없이 데이터 자체에서 클록을 추출한다 — 시스템 비용은 줄지만 RX의 CDR 설계 복잡도가 크게 증가한다. **오늘날 대부분의 고속 SerDes가 채택하는 표준 방식**이다.

## CDR 아키텍처 3분류

```text
1. Phase Tracking CDR   : 피드백 루프로 위상 정렬 — PLL/DLL/PI/ILO 기반
2. Open-Loop CDR        : 피드백 없음 — Gated Oscillator 등
3. Oversampling-based CDR: 위상 정렬 자체를 포기 — Blind Oversampling
```

### Phase Tracking CDR

**PLL 기반 (외부 기준 클록 사용)**: 주파수 추적(coarse)과 위상 추적(fine) 두 개의 트래킹 루프를 사용한다(두 VCO가 제어 전압을 공유하면 미스매치 문제로 큰 오버헤드 발생). Lock detector로 주파수/위상 추적 루프를 순차적으로 활성화하면 하드웨어 오버헤드를 줄일 수 있다.

**Referenceless CDR (외부 기준 클록 없음)**: 기준 클록 대신, 주파수 검출기(frequency detector)가 랜덤 데이터로부터 직접 주파수 정보를 추출한다 — 설계가 훨씬 까다롭다.

**DPLL(Digital PLL) 기반**: 디지털 루프 필터로 레이아웃 면적을 줄이고 PVT 변화·누설(leakage) 문제를 완화하지만, 루프 지연(latency)과 양자화 노이즈 문제가 있다.

**DLL 기반**: 다중 VCO 미스매치와 높은 전력 소모라는 PLL의 단점을 피하고, 지터 누적(jitter accumulation)이나 루프 안정성 문제가 없다. 다만 위상 포착 범위가 제한적이고 지터 필터링이 없으며, stuck locking과 harmonic locking 문제가 있다. 주파수 오프셋($\Delta f$)이 있으면 지연 범위 한계로 무한히 추적할 수 없다는 근본적 약점이 있다.

**PI(Phase Interpolator) 기반**: DLL 기반과 유사하게 안정적인 루프 특성을 가지면서도, 일정한 주파수 오프셋을 어느 정도 보상할 수 있다 — **최근 디지털 CDR 시스템에서 가장 널리 쓰이는 구조**다. 다만 선형성과 다중 위상 생성의 이슈가 있다.

### Open-Loop CDR: Gated Oscillator

빠른 동기 클록 복원과 데이터 획득이 가능하고 설계가 단순·소형이지만, 지터 제거(jitter rejection) 능력이 없고 PVT 변화에 민감하다.

### Oversampling-based CDR: Blind Oversampling

수신된 각 데이터 비트를 여러 지점에서 샘플링한다 — 피드백이 없어 빠른 포착(acquisition)과 본질적인 안정성을 갖지만, 많은 샘플링 회로·다중 위상 클록·대형 데이터 레지스터가 필요해 고속에서는 비현실적이다.

## CDR Phase Detector (PD)

CDR과 PLL의 근본적인 차이: **PLL의 입력은 주기적인 클록**이지만, **CDR의 입력은 비주기적인 랜덤 데이터**다. 따라서 PD는 전이(transition)가 누락된 경우에도 올바르게 동작해야 한다.

### Simple PD (동기식 엣지 검출)

출력의 평균값이 데이터 전이 밀도(transition density)의 함수가 된다 — 위상차가 2배로 커지고 전이 밀도가 같은 비율로 줄면 평균 출력이 변하지 않는다는 한계가 있다.

### Hogge PD (Linear PD)

**선형** 위상검출기로, 오차 신호(ERR)의 폭이 위상 오차에 비례한다(REF의 폭은 항상 half clock cycle로 고정). 클록이 이르면(early) ERR이 REF보다 좁고, 늦으면(late) ERR이 REF보다 넓다.

### Alexander PD (Bang-Bang PD, BBPD)

**이진(binary)** PD — 위상 오차의 **부호(sign)만** 검출한다(크기는 모름). 2× 오버샘플링(데이터 샘플 + 엣지 샘플)으로 구현되며, 구조가 단순하고 고속 동작에 강건해 **고속 SerDes에서 사실상 표준**으로 쓰인다. 다만 클록 지터에 의해 BBPD 특성 곡선이 smoothing되며, 작은 위상차에서의 PD 이득이 랜덤 지터의 표준편차에 반비례한다.

| PD 유형 | 출력 | 장점 | 단점 | 주 용도 |
|---|---|---|---|---|
| Linear PD (Hogge) | 위상오차에 비례 | 정교함 | Transition density 문제, 고속에서 어려움 | 저속 |
| Bang-Bang PD (Alexander) | 부호만(Early/Late) | 고속에 강함, 단순 | 지터에 민감 | 고속 SerDes(대부분) |

### PAM4 CDR Phase Detector

PAM4는 3개의 임계 레벨이 필요하며, 2× 오버샘플링에서 모든 전이를 포착하려면 5개의 엣지 샘플러가 필요하다(half-rate 기준 총 6개 데이터 + 10개 엣지 샘플러).

전이 밀도(transition density)는 사용하는 엣지 샘플 수에 따라 달라진다.

| 엣지 샘플 수 | 전이 밀도 | 비고 |
|---|---|---|
| 5개 | 75% | 하드웨어 오버헤드 큼 |
| 3개 | 50% (NRZ 수준) | - |
| 1개 | 25% | 밀도 부족 |

샘플러 1개만으로도 "좋은 전이(good transition)"와 "나쁜 전이(bad transition)"를 구분해 매우 이르거나(very early) 매우 늦은(very late) 신호를 생성함으로써, 적은 샘플러 수로도 유사한 성능을 얻을 수 있음이 연구로 확인되었다(Shahramian, ISSCC'19). 임계 레벨 수(1개 vs 3개)에 따라 2-level transition elimination의 유불리가 갈린다 — 채널 손실이 낮을 때와 높을 때 최적 전략이 다르다(Verbeke, TCAS-I'21).

## Jitter in High-Speed Links

| 소스 | 설명 |
|---|---|
| Data-dependent jitter | 손실 채널로부터 발생 (ISI 또는 crosstalk) |
| Source jitter | 클로킹 회로의 RJ/DJ, PSIJ(전원 유도 지터) |
| CDR jitter | 클록 복원 블록(VCO 등)에서 자체 생성 |

### CDR Jitter Metrics

| 지표 | 정의 | 설계 포인트 |
|---|---|---|
| **Jitter Generation (JGEN)** | 지터 없는 입력에 대해 CDR 출력이 갖는 지터 | PLL/VCO, PSIJ 최소화. (SONET 요구: JGEN < 10mUIrms) |
| **Jitter Transfer (JTRAN)** | 입력 정현파 지터 대비 출력 지터의 비율 | Jitter transfer bandwidth와 peaking으로 규정 (SONET OC-192: peaking < 0.1dB) |
| **Jitter Tolerance (JTOL)** | 특정 BER을 만족하며 버틸 수 있는 최대 입력 지터 | CDR의 tracking 능력을 나타내는 핵심 성능 지표. BERT(예: Anritsu MP1800A)로 측정 |

JTOL은 CDR 트래킹 대역폭을 넘어서면 −20dB/decade 기울기로 허용치가 감소하는 마스크(mask) 형태로 규정된다.

## Referenceless CDR: 외부 기준 클록 없이

외부 기준 클록을 없애면 시스템 비용이 줄고(리피터, 액티브 케이블 등에 적합) 광범위한 연속 데이터 레이트(continuous-rate) 동작이 가능해지지만, 주파수 정보를 오직 입력 랜덤 데이터에서만 얻어야 한다.

### Dual-Loop 구조

위상 획득과 주파수 획득을 위한 두 개의 루프로 구성 — 별도의 주파수 검출기가 필요하고, 두 루프 사이의 간섭(interference)이 발생할 수 있다.

| Frequency Detector | 원리 |
|---|---|
| **Pottbacker FD** (JSSC'92) | 두 개의 double-edge triggered flip-flop + 일반 flip-flop으로 clock fast/slow를 판별 |
| **Rotational FD** (Dalton, JSSC'05) | 위상 다이어그램 상에서 데이터 엣지의 회전 방향을 검출($f_C<f_D$면 Freq Up, $f_C>f_D$면 Freq Down) |
| **자동 주파수 획득** (Shu, JSSC'16) | BBPD의 연속 출력 개수에 반비례하는 주파수 오차 특성을 이용. Threshold 설정이 FLL 락킹과 잔여 주파수 오차 사이의 트레이드오프를 만든다 |

### Single-Loop 구조

위상과 주파수 획득을 하나의 Phase-Frequency Detector(PFD)로 통합 — 하드웨어 오버헤드가 작고 루프 간섭이 없다.

| PFD | 원리 |
|---|---|
| **Non-zero Strobe Point Linear PD** (Huang, JSSC'15) | 게인 곡선에 오프셋을 유발하되 작은 하드웨어 오버헤드로 구현. 포착 범위가 좁고 주파수 획득이 느림 |
| **Unilateral BBPD** (Chen, TCASI'14) | Phase roller가 위상차 윈도우에 따라 BBPD 출력을 스왑. 복잡한 스위칭 회로와 높은 전력 소모 |
| **Multi-Phase Oversampling PFD** (K. Park, JSSC'18) | 두 BBPD(CK0/180용, CK90/270용)의 출력으로부터 다중 위상 클록의 드리프트 방향을 읽어 주파수 정보를 얻음. 단, 포착 범위가 제한적이고(주파수 오차가 범위를 넘으면 락 실패), 클록 위상·샘플러가 2배로 늘어 전력 오버헤드가 큼 |
| **Stochastic PFD** (K. Park, JSSC'22) | "기존 BBPD와 동일한 정보만으로 PFD를 구현할 수 있는가?"라는 질문에서 출발 — 연속된 data/edge 샘플의 시퀀스 패턴(histogram)을 모니터링해 위상과 주파수 정보를 동시에 얻음. 확률적 설계 방법론(4단계: 히스토그램 수집 → 대표 히스토그램 선택 → 가중치 계산 → PD/FD 게인 곡선 평가)으로 각 3-bit 시퀀스 패턴에 가중치를 부여 |

## Baud-Rate CDR: 왜 필요한가

**2× Oversampling CDR**은 심볼당 2개 샘플(데이터+엣지)이 필요하다 — half-rate 클로킹이면 4개의 클록 위상이 필요해, 다중 위상 생성·분배에 큰 전력이 소모된다.

**Baud-rate Sampling**은 심볼당 1개 샘플만 사용한다 — half-rate 클로킹에 차동 클록만 있으면 되므로 클록 위상 수와 샘플러 수가 줄어 **전력 효율이 훨씬 좋다**.

### Baud-Rate CDR의 근본적 어려움

데이터 샘플만으로는 위상 차이 정보를 얻을 수 없다 — early clock과 late clock에서 동일한 data/error 샘플이 나올 수 있기 때문에, **기울기(slope) 같은 추가 정보**가 필요하다.

### Mueller-Muller Phase Detector (MMPD)

심볼당 샘플 1개만 사용하는 baud-rate PD로, 2× 오버샘플링보다 에너지 효율적이다. Lock point는 $h_{-1}=h_1$(첫 pre-cursor와 post-cursor가 같아지는 지점)이다. 2× oversampling BBPD가 엣지 샘플로 위상을 검출하는 것과 달리, MMPD는 **데이터 샘플 시점의 신호 레벨**을 기준으로 동작한다 — $0\to1$ 전이에서 $V[0]>|V[1]|$이면 "Early", $V[0]<|V[1]|$이면 "Late".

### DFE와의 상호작용 문제 (Unequalized MMCDR)

MMPD는 $h_{-1}=h_1$을 강제하지만, DFE는 $h_1=0$을 강제한다 — 두 조건이 충돌하며 **CDR lock point가 왼쪽으로 시프트**되어 마진이 줄고, lock point가 노이즈에 민감해진다. CTLE에 별도 샘플러를 추가해 CDR과 DFE 적응을 분리하는 방법은 비현실적이며, **Unequalized MMCDR**(Dokania, ISSCC'15)이 이 문제를 해결한다.

### Sign-Sign MMPD (SSMMPD)

MMPD 기반이지만 샘플의 부호만 사용해 구현을 단순화한다(연속된 두 샘플 활용, UI당 3샘플) — Spagna, ISSCC'10.

### 최신 연구: 2UI Integrator 기반 패턴 CDR (S. Kang, JSSC'25)

기존 baud-rate CDR(데이터-레벨 기준 사용)은 추가 하드웨어(전력·면적 증가)와 다중 루프 간섭(suboptimal lock point) 문제가 있다. 제안 구조는 **데이터-레벨 기준 없이** 동작한다:

- 2UI 적분기(integrator)가 Reset/Integration/Hold 3상태로 동작한다.
- $a_n$과 $a_{n+1}$이 다르면, $T_n$이 '1'로 샘플링될 확률이 50%가 된다는 통계적 성질을 이용한다.
- 데이터 샘플과 2UI 적분 샘플로 위상 정보를 얻으며, **zero-crossing 샘플러만** 사용한다.
- 기존 SS-MMPD 대비 더 높은 PD 게인과, 다양한 채널 조건에서 더 강건한 동작을 달성했다.

## 시험·복습 체크포인트

- Synchronous, Mesochronous, Plesiochronous의 차이와 각각에 필요한 회로는?
- PLL 기반, DLL 기반, PI 기반 CDR의 장단점, 특히 왜 PI 기반이 최근 표준이 되었는가?
- Hogge PD(Linear)와 Alexander PD(Bang-Bang)의 차이, 그리고 고속에서 BBPD가 선호되는 이유는?
- JGEN, JTRAN, JTOL이 각각 무엇을 측정하며, 왜 JTOL이 CDR의 핵심 성능 지표인가?
- Referenceless CDR에서 Dual-loop와 Single-loop(PFD) 구조의 트레이드오프는?
- Baud-rate CDR이 2× oversampling CDR보다 전력 효율적인 이유, 그리고 위상 정보를 얻기 위해 추가 정보(slope)가 필요한 이유는?
- MMPD의 lock point 조건과, DFE와 결합했을 때 발생하는 lock point shift 문제를 설명할 수 있는가?
{% endraw %}

---

이전: [06. 수신단 이퀄라이저(CTLE·DFE)와 적응](06-rx-equalization-and-adaptation.md) · 다음: [08. 최신 연구 동향: 세미나·논문 리뷰 종합](08-advanced-research-trends.md)
