---
layout: page
title: "08. 최신 연구 동향: 세미나·논문 리뷰 종합"
permalink: /grad_studies/high_speed_serial_link_circuits/08-advanced-research-trends/
sitemap: false
---

- **강의**: Exam 복습(9주) · Invited Talk 1~2(10~11주) · Final Presentation(12주 이후) 종합

{% raw %}
## 개요

이 장은 특정 강의 슬라이드가 아니라, 시험 주간 이후 진행된 **학계·산업 초청 세미나**와 **학생 논문 리뷰·연구 발표**를 종합한 것이다. Ch.1~7이 SerDes의 표준적인 교과서 지식을 다뤘다면, 이 장은 2025~2026년 시점 **고속 인터페이스·혼성신호 회로 연구의 최전선**을 보여준다. Final Presentation의 공식 주제는 (1) High-Speed Transmitter, (2) High-Speed Receiver, (3) High-Speed CDR, (4) Single-Ended Parallel Transceiver 네 갈래였으며, 여기에 초청 세미나의 DSP/6G 통신 주제와 학생들이 자유롭게 선택한 뉴럴 인터페이스·데이터 컨버터·검증·AI 설계자동화 주제가 더해졌다.

```text
Part 1. DSP 기반 차세대 유무선 통신 (초청 세미나: 학계)
Part 2. 고성능 데이터 컨버터
Part 3. CDR·이퀄라이저 적응 심화 연구
Part 4. 특수 시그널링과 크로스토크 캔슬레이션
Part 5. 광인터커넥트 수신기
Part 6. 뉴럴 인터페이스 회로
Part 7. 검증·테스트와 AI 설계 자동화
Part 8. 최신 PAM-4 수신기 슬라이서
Part 9. UWB Mismatch Calibration
```

## Part 1. DSP 기반 차세대 유무선 통신

과거에는 유선(Wireline)과 무선(Wireless) 통신의 설계가 분리되어 있었지만, 데이터 레이트가 급증하면서 두 분야 모두 거대한 **디지털 신호 처리(DSP)** 블록을 포함하는 방향으로 수렴하고 있다. 공정이 미세화될수록(예: 3nm) 아날로그 회로의 성능 개선은 한계에 부딪히지만, 디지털 로직은 스케일링의 이점을 그대로 누릴 수 있기 때문이다.

### 유선(Wireline): 데이터센터용 DSP 이퀄라이저

데이터센터 서버 간 연결에서 112Gbps 이상의 고속 전송 시 채널 손실은 최대 40dB에 달할 수 있다 — 기존 아날로그/혼성신호 리시버로는 노이즈와 대역폭 한계 때문에 복원이 어렵다. 이에 아날로그 신호를 ADC로 즉시 디지털화한 뒤 모든 처리를 디지털 도메인에서 수행하는 **ADC 기반 리시버**가 주류가 되고 있다(Ch.1의 ADC 기반 아키텍처와 직결).

- **Fractional-Spaced FFE**: 위너 필터(Wiener filter) 이론에 기반해 최소 평균제곱오차(MMSE)가 되도록 필터 가중치를 적응시킨다. 데이터의 자가상관행렬(auto-correlation matrix)의 고유값·고유벡터를 분석해, 복잡한 아날로그 타이밍 피드백 루프 없이도 위상 변화를 추적할 수 있다 — 즉 **디지털 CDR의 역할**을 대신 수행한다.
- **Sliding-Block DFE**: 기존 DFE의 타이밍 병목(피드백이 1UI 안에 끝나야 함)과, Loop Unrolling의 탭 수 증가에 따른 지수적 MUX 지연 문제를 해결하기 위해, 피드백 루프를 끊고 feed-forward 방식으로 블록 단위 처리를 한다. **마르코프 체인(Markov Chain)** 이론에 따라, 초기 스테이지의 에러율은 50%에 달하지만 여러 스테이지를 거치며 에러 확률이 급격히 낮아져 최종적으로 안정적인 결정에 도달한다.
- **MLSD(Maximum Likelihood Sequence Detection, Viterbi Algorithm)**와 **신경망 기반 이퀄라이제이션**: 통신이론의 고전적 기법과 딥러닝이 공정 미세화 덕분에 고속 유선 통신에도 적용 가능해지고 있다.

### 무선(Wireless): 6G와 MIMO DSP

6G 시대의 THz 대역·저궤도 위성 통신에서는 채널 상태가 극도로 빠르게 변하고 안테나 수가 크게 증가한다(예: 1024개). 신호 도달 각도(Direction of Arrival)를 찾는 소스 로컬라이제이션은 안테나 수 $N$에 대해 공간 자가상관행렬 연산이 $O(N^2)$ 또는 $O(N^3)$의 막대한 복잡도를 가진다.

- **분산 처리(Distributed Processing)**: 큰 행렬을 중앙에서 계산하는 대신 작은 로컬 프로세서로 분할하고, 프로세서 간 데이터를 교환하며 전역 평균 수렴 알고리즘으로 국소 연산만으로 글로벌 솔루션을 찾는다.
- **하이브리드 방향 탐지 (Model-based + CNN)**: 순수 모델 기반은 $O(N^2)$로 안테나가 많아지면 확장 불가능하고, 순수 데이터 기반(CNN)은 이상적이지 않은 환경에도 학습으로 대응할 수 있다. **하이브리드 방식**은 정보량이 많은 교차상관(cross-correlation) 특성으로 입력 차원을 $O(N)$(선형)으로 축소한 뒤 고정된 CNN에 학습시켜, 안테나 수가 늘어도 연산량 증가를 억제하면서 높은 정확도를 얻는다.

> **Q&A 핵심**: 데이터센터 DSP는 현재 PCIe 5.0/6.0, 고속 SSD 인터페이스에 적용 중이며, 향후 HBM 등 차세대 메모리 인터페이스에도 탭 수 감소 형태로 적용될 가능성이 크다. 무선 MIMO는 여러 안테나 신호가 공간적으로 강하게 간섭해 복잡한 분리 연산이 필요한 반면, 유선 메모리(HBM)는 핀당 데이터가 상대적으로 독립적인 채널(single-ended)로 들어와 접근 방식이 다르다.

## Part 2. 고성능 데이터 컨버터

### Zoom ADC — 고해상도·저전력 ADC

자동차·의료기기·IoT 분야는 제한된 전력으로 충분한 대역폭과 고해상도를 동시에 요구한다.

| ADC 종류 | 특징 |
|---|---|
| Flash ADC | 매우 빠르지만 $N$비트 해상도에 $2^N-1$개 비교기 필요 → 전력·면적 기하급수적 증가 |
| SAR ADC | 에너지 효율 우수, 중간 해상도에 적합하나 커패시터 미스매치로 고해상도에 한계 |
| Delta-Sigma ($\Delta\Sigma$) ADC | 오버샘플링 + 노이즈 쉐이핑으로 고해상도 가능하나 대역폭 제한·필터 설계 부담 |

**Zoom ADC**는 Coarse(빠르고 낮은 해상도) → Residue 생성($V_{in}$ - coarse 코드) → Fine(작아진 residue를 $\Delta\Sigma$로 정밀 변환)의 2단계로, 신호 범위가 작아진 만큼 Fine 단의 부담을 줄인다. 최신 연구는 Coarse 단에 SAR 대신 **CT(Continuous-Time) $\Delta\Sigma$ Modulator**를 사용해, coarse 단 양자화 노이즈를 고주파로 밀어내고(noise shaping) LPF로 제거함으로써 zoom-in 계수와 선형성 캘리브레이션 필요성을 크게 줄였다 — 1.5비트 양자화기로 SNDR 91.2dB, 전력 392µW를 달성했다.

### Time-Interleaved ADC 캘리브레이션

다수의 서브 ADC를 병렬화해 속도를 높이는 Time-Interleaved ADC는 채널 간 샘플링 클록 타이밍 스큐($\Delta t$)가 SNR/SNDR을 심각하게 열화시킨다.

- **Stochastic 검출의 한계**: 오토코릴레이션이나 MAD(Mean Absolute Difference)로 에러를 검출하는데, 입력 신호 주파수가 나이퀴스트 대역의 절반($f_s/4$)을 넘으면 미분값 부호가 바뀌어 정상적인 negative feedback 제어가 불가능해지는 **대역폭 제한 문제**가 있다.
- **대역폭 제한이 없는 4채널 캘리브레이션**: 모든 서브 채널 간 진폭 차이를 선제적으로 계산해 평균을 낸 뒤 매 사이클 빼주는 구조 — 연산 로직은 다소 증가하지만 대역폭 제한을 나이퀴스트 주파수($f_s/2$)까지 확장하고 캘리브레이션 사이클을 단축한다.
- **Limit Cycle Oscillation**: Sign-Sign LMS 적용 시 디지털 로직 분해능 한계로 수렴점에서 특정 코드가 반복 진동할 수 있어, 이를 막기 위한 히스테리시스(hysteresis) 밴드 적용이 필요하다.

## Part 3. CDR·이퀄라이저 적응 심화 연구

### CTLE Adaptation: 시간 도메인에서 주파수 응답을 어떻게 간접 측정할 것인가

DFE는 Sign-Sign LMS로 비교적 쉽게 적응을 구현할 수 있지만, CTLE는 시간 도메인에서 주파수 응답을 직접 측정하기 어렵다.

- **Pulse Charging 기법** (2017): 데이터 피크 근처에 기준 전압을 설정하고, 입력이 이를 교차할 때 Up/Down 펄스를 생성해 두 펄스 비율($\alpha:1$)을 측정한다. 비율을 2:1로 유지하도록 CTLE의 degeneration 커패시터를 제어하면 아이 오프닝이 최대화됨을 시뮬레이션으로 확인했다.
- **Counter 기반 기법**: StrongARM 래치가 under-equalized(피크 전압 부족) 또는 over-equalized(AC peaking 과다로 DC 레벨 감소) 상태일 때 뒷단 샘플러 출력이 정상 로직 레벨로 벌어지지 못하는 특성을 이용한다. 이런 "분리되지 않는" 에러 펄스 개수를 카운트해 최적 CTLE 코드를 찾는다 — 복잡한 연산기 없이 단순 카운터 로직만으로 전력·면적 효율이 매우 우수하다.

### Maximum Eye-Tracking (MET) CDR

Baud-rate CDR(Ch.7)의 대표 PD인 MMPD는 DFE와 결합할 때 lock point가 최대 아이 마진 지점에서 벗어나는 문제가 있었다.

- **기존 MET CDR의 한계**: 아이 마진이 최대(기울기 0)가 되는 지점을 찾기 위해 여러 클록 위상 또는 디더링(dithering)을 사용하지만, 적응 알고리즘이나 오프칩 트레이닝으로 인해 위상 추적 시간이 수십 µs~수백 ms로 매우 느리다.
- **제안: SAR Logic 기반 MET CDR**: SAR(Successive Approximation Register) 로직으로 클록 위상을 조절한다. 현재 위상에서 클록을 $1/32$UI(7bit PI 기준) 이동시켜 데이터 레벨(dLev) 증감으로 방향을 판별한 뒤, $1/2, 1/4, 1/8$UI 등 SAR 스텝으로 점차 크기를 줄이며 수렴한다. 단 6~7번의 위상 이동만으로 1.67µs 안에 최대 아이 지점에 도달하며, Eye Height가 50mV → 124mV로 크게 개선되었다.

## Part 4. 특수 시그널링과 크로스토크 캔슬레이션

Final Presentation 공식 주제 "Single-Ended Parallel Transceiver"와 직결되는 영역이다. Single-ended 병렬 인터페이스는 die-to-die 통신에서 높은 핀 효율 때문에 널리 쓰이지만(예: 기존 메모리 인터페이스, UCIe 1.1 Chiplet PHY), common-mode noise(CMN)·crosstalk·SSN(Simultaneous Switching Noise)에 취약해 더 높은 신호 무결성을 위한 시그널링 기법이 필요하다.

### Pseudo-Differential Signaling

Single-ended는 기준 노이즈에 취약하고, Differential은 $N$비트에 $2N$개 채널이 필요해 라인 효율이 떨어진다. Pseudo-Differential은 TX에서 인접 데이터 간 **전이(transition) 유무**를 기준으로 인코딩하고, RX에서 두 개의 CML 버퍼 + Transition Check Circuit(TCC)로 인접 데이터가 같은 레벨인지 다른 레벨인지 판별해 differential 신호로 복원한다 — $N$개 핀으로 $N-1$개 데이터를 안정적으로 전송한다.

### Chord Signaling (IS-Ratio 최적화)

PAM4 등 멀티레벨 신호는 샘플러 기준에 따라 심볼 간 거리가 달라져 ISI-Ratio(최대거리/최소거리)가 1이 되지 않는 문제가 있다. **Walsh-Hadamard 변환(WH-Transformation)** 같은 직교 행렬로 데이터를 여러 와이어에 분산 인코딩해 전송하고 RX에서 조합하면, 수신단 샘플러 기준에서 모든 심볼 코드가 동일한 거리 $D$를 갖게 되어 ISI-Ratio=1을 달성하고 에러에 강건해진다.

### Crosstalk Cancellation

멀티레벨 신호는 신호 레벨 간 margin이 줄어드는 만큼 crosstalk에도 더 취약해진다.

$$
V_{margin,NRZ} = V_H - V_{XT}, \quad V_{margin,PAM3} = 0.5V_H - V_{XT}, \quad V_{margin,PAM4} = 0.33V_H - V_{XT}
$$

**DFE 기반 Crosstalk Cancellation** (Nazari, JSSC'12): Aggressor 라인에서 Victim 라인으로 넘어오는 간섭은 미분($dV/dt$) 형태를 띤다. Aggressor 신호를 RC High-pass Filter($H(s)\approx sRC$)에 통과시켜 이 미분 파형을 모방(mimic)한 뒤, RX의 Switched-Capacitor Summer에서 DFE 연산과 동시에 이 모방 성분을 빼주어 crosstalk를 제거한다 — 패시브 RC 소자만 사용해 전력 소모를 크게 낮춘다.

## Part 5. 광인터커넥트 수신기 (Optical Receiver)

### CMOS APD 기반 광수신기 최적화

공정 스케일다운(28nm 등)에 따라 포토다이오드(PD) 성능이 하락하는 문제를 회로적으로 보상한다.

- **APD 특성 분석**: Silicon Avalanche Photodiode(Si-APD)의 바이어스 전압에 따라 대역폭이 최대가 되는 전압(10.2V)과 SNR이 최대가 되는 전압(9.4V)이 서로 달랐다 — 통신 신뢰성을 위해 **SNR 최대 전압(9.4V)**을 최적 바이어스로 선정했다.
- **회로적 보상**: 부족한 PD 대역폭(5.66GHz)을 TIA(Transimpedance Amplifier)에 **Inductive Peaking**을 적용해 전체 대역폭을 10.9GHz까지 확장, 20Gbps 속도를 달성했다.

## Part 6. 뉴럴 인터페이스 (Brain-Computer Interface) 회로

### 신경 신호의 특성

| 신호 | 대역 | 진폭 | 특징 |
|---|---|---|---|
| EEG (두피 뇌파) | - | - | 감쇠가 크고 넓은 영역 신호가 섞임 |
| LFP (Local Field Potential) | 1Hz~200Hz | 2.1~5mV | 뉴런 집단 활동 |
| AP (Action Potential) | 100Hz~5kHz | 10~500µV | 개별 뉴런 스파이크, 매우 작은 진폭 |

**폐루프(Closed-loop) 뉴럴 레코딩**은 신호를 읽고 분석한 뒤 필요시 전기 자극(stimulation)을 가한다 — 자극 시 발생하는 수백mV 이상의 **Stimulation Artifact**와 움직임·전극 임피던스 변화에 의한 **Motion Artifact**를 수µV 수준의 신경 신호와 포화 없이 동시에 처리해야 하며, 전극 신호 손실을 막기 위해 높은 입력 임피던스가 요구된다.

### AFE(Analog Front-End)와 ADC 구조

| 접근 | 장단점 |
|---|---|
| High-Gain LNA + ADC | 40dB+ 게인으로 노이즈(NEF≤5) 우수하나, 큰 아티팩트에 바로 포화 → 폐루프에 부적합 |
| Direct Digitization (LNA 없음) | 큰 신호도 잘 처리(DR 높음)하나 IRN·NEF 열화 |
| **$G_m$-C 기반 CT-DSM** | 게이트로 입력을 받아 임피던스가 거의 무한대. Source Degeneration + I-DAC 피드백으로 residue 신호만큼만 스윙을 제한해 선형성 확보 → 300mVpp 입력(아티팩트 포함)에서도 80dB+ SNDR. VCO 기반 적분기·양자화기로 2차 DSM 구현 |

- **EDL(Electrical Double Layer) 센싱**: 세포의 미세 전압을 직접 읽는 대신, 뇌 활동(이온 이동)에 따라 전극 표면에서 변화하는 EDL 커패시턴스 변화량을 측정한다.
- **CDM(Code Division Multiplexing)**: $N$개 채널을 개별 앰프로 읽지 않고, 직교(orthogonal) 코드를 부여해 $2\sqrt{N}$개의 적은 인터커넥트만으로 다중 채널을 복원한다. 신호는 변조되지만 플리커(1/f) 노이즈는 변조되지 않아 효과적으로 걸러진다.
- **SPA(Switched-Capacitor Pre-Amplifier)**: 커패시터를 병렬→직렬로 스위칭해 전하량 보존 법칙으로 전압 이득을 얻는다. Z-boosting 오토 캘리브레이션 루프로 기생 커패시턴스에 의한 이득 하락을 방지하며, **NEF 1.25**라는 매우 우수한 노이즈 효율을 달성했다.

### 대규모(High-Channel-Count) Neural Interface: Sparse/Event-Driven Readout

채널 수가 수천~수백만 개로 증가하면서 **pixel size, routing, power, data-rate, wireless bottleneck**이 동시에 문제가 된다. 예를 들어 1M 채널을 가정하면 raw data rate는 약 200Gbps에 달해, wired link로도 부담스럽고 implantable 기기의 wireless link로는 더더욱 불가능하다.

> **핵심 철학의 전환**: "각 채널을 어떻게 읽을 것인가"에서 "어떤 데이터를 버리고 어떤 데이터만 보낼 것인가"로.

신경 신호는 **희소성(sparsity)**을 가진다 — 대부분의 채널은 공통 baseline 근처에서 작은 변화만 보이고, 의미 있는 activity는 일부 채널에서 간헐적으로만 발생한다.

| 사례 | 구조 | 효과 |
|---|---|---|
| **Single-slope ADC 기반 sparse readout** | 각 pixel엔 amplifier+comparator만, ramp/counter/readout logic은 공유. Common-level 신호는 버리고 unique 신호만 전송 | Energy 절감 약 20배, data-rate 감소 약 150배 (1024ch×8bit 163Mbps → 약 1Mbps) |
| **Event-driven threshold detection + reconfigurable ADC** | 평소엔 각 pixel이 inverter+capacitor로 threshold crossing만 감지, activity 발생 영역만 묶어 flash ADC처럼 재구성(예: 64pixel을 4영역×16inverter로) | Pixel size·전력·data-rate 모두 감소, 필요시 4~8bit로 adaptive resolution |

두 사례 모두의 결론은 같다 — **ADC 하나의 resolution/FoM을 극단적으로 최적화하는 것이 아니라, architecture와 algorithm을 함께 설계해 필요한 정보만 뽑아내는 것**이 대규모 뉴럴 인터페이스의 핵심이다.

## Part 7. 검증·테스트와 AI 설계 자동화

### 3D IC 및 멀티 다이(Multi-Die) 패키지 테스트 (DFT)

3D 적층 IC(HBM, 칩렛)는 물리적 프로빙이 불가능한 내부 인터커넥트를 가지므로, 설계 단계부터 테스트 용이성(DFT)을 고려하는 **IEEE 1838** 등 표준 적용이 필수다.

- **I/O 루프백(Loop-back) 패스 삽입**: Wrapper cell 내부에 루프백 테스트 경로를 추가해, 결함이 하단 다이(Die 1)/상단 다이(Die 2)/마이크로 범프(Bump) 중 어디에 있는지 격리·판별한다.
- **범프 물리적 거리 기반 ATPG**: 소프트웨어적 로직 테스트 패턴에 3D 범프의 실제 물리적 배치 정보를 추가로 인가한다 — 인접한 범프일수록 쇼트(short) 결함 확률이 높다는 현실을 반영해 테스트 커버리지를 극대화한다.

### AI 기반 RF 프론트엔드 역설계 (Inverse Design)

전통적 RF·안테나 설계는 설계자의 직관 + 반복적인 풀웨이브 EM 시뮬레이션이라는 비효율적 방식이었다. 최근 생성형 AI와 강화학습이 적극 도입되고 있다.

- **Tandem Neural Network & Diffusion Model**: 목표 스펙트럼(예: $S_{11}$)을 입력하면 픽셀화된 비정형 RF 구조를 즉시 생성한다. Diffusion 모델은 랜덤 노이즈에서 구조를 점진적으로 생성하므로 동일 목표 성능을 만족하는 **다양한 구조적 대안(multi-solution)**을 탐색할 수 있다.
- **RL(강화학습) 기반 PA/LNA 설계**: AI 에이전트가 회로 토폴로지·트랜지스터 사이징·바이어스를 직접 선택하고, 목표 성능(gain/power/efficiency) 근접도에 따라 보상을 받아 학습한다 — 주어진 공정(PDK) 내 최적 성능 경계(Pareto Front)를 빠르게 탐색한다.
- **AR(Auto-Regressive) Transformer**: LLM이 이전 단어 문맥으로 다음 단어를 생성하듯, 타겟 스펙트럼 정보를 바탕으로 안테나 픽셀을 순차적으로 예측·생성한다 — 기존 유전 알고리즘(GA) 기반 탐색보다 훨씬 빠르고 강력한 다중 해 생성 능력을 보인다.

## Part 8. 최신 PAM-4 수신기: Track-and-Regenerate 슬라이서

64Gbps 이상으로 데이터 레이트가 증가하면서, Ch.4에서 다룬 StrongARM 기반 슬라이서의 근본적 한계가 다시 부각된다. PAM4는 NRZ 대비 레벨 간 진폭 차이가 훨씬 작아 노이즈·ISI에 더 취약하고, DFE의 1UI 타이밍 병목(Ch.6)이 더욱 치명적이다.

**제안: Track-and-Regenerate Slicer**: 기존 StrongARM 래치는 매 사이클 내부 노드를 리셋해야 해 속도에 한계가 있다. 새 구조는 리셋 과정 없이, **트래킹 모드**에서 입력을 연속적으로 따라가다가 **Regeneration 모드**에서 positive feedback으로 미세한 전압 차이를 초고속 증폭한다 — 슬라이서 지연을 기존 대비 절반 수준으로 줄여 DFE 타이밍 제약을 크게 완화하고, 60Gbps 이상에서도 넓은 아이 오프닝과 안정적인 BER을 달성했다.

## Part 9. UWB Mismatch Calibration (LO Phase Switching)

UWB(Ultra-Wideband) transceiver에서는 LO phase mismatch, I/Q mismatch, ADC mismatch, TX/RX path mismatch가 SSB tone 생성·변조 정확도·이미지 억제·캘리브레이션 정확도에 영향을 준다.

**제안 구조**: 복잡한 아날로그 캘리브레이션 회로를 추가하는 대신, **위상 스위칭(phase switching) + 디지털 제어**로 캘리브레이션에 필요한 tone(SSB tone)을 생성한다 — $n$개의 phase 후보를 두고 전체 주기 $T$를 $n$등분해 순차적으로 선택한다.

| Q&A 쟁점 | 핵심 |
|---|---|
| 왜 $n=4$인가 | Phase resolution(정확도) 증가와 hardware overhead(면적/전력) 증가 사이의 균형점 |
| Foreground인가 Background인가 | Foreground calibration에 가까움(동작 전에 한 번 수행) — 온도/전압 변화에 대한 실시간 대응은 상대적으로 약할 수 있음 |
| TX/RX ADC mismatch 처리 | 제안 구조 자체가 모든 mismatch를 해결하는 것은 아니며, SSB tone을 생성할 수 있어 **기존 ADC mismatch calibration 기법과 결합** 가능하다는 것이 강점 |
| Phase switching 속도 | LO는 6.5~8GHz로 높지만, phase selection 제어는 10MHz 기준(100ns 안에 4회 switching)이면 충분 — 현실적으로 구현 가능한 속도 |

## 최신 연구 동향을 관통하는 공통 메시지

이 학기 후반부의 모든 발표를 관통하는 문제의식은 Ch.1~7에서 배운 원리들이 실제 연구에서 어떻게 "트레이드오프의 최적점"을 찾는 문제로 반복되는지를 보여준다.

1. **정확도-속도-전력의 3자 트레이드오프는 어디에나 있다** — CTLE peaking(정확도 vs 노이즈), DFE 타이밍(속도 vs 하드웨어), ADC 해상도(정확도 vs 전력), 뉴럴 인터페이스 readout(정보량 vs 데이터레이트) 모두 같은 구조다.
2. **"측정하기 어려운 것"을 "측정 가능한 대리 지표"로 바꾸는 것이 혁신의 핵심**이다 — CTLE의 주파수 응답 대신 카운터로 에러 펄스를 세거나, baud-rate PD가 위상차 대신 신호 레벨을 보거나, sparse neural readout이 원시 신호 대신 "변화가 있었는가"만 보내는 것 모두 같은 아이디어의 변주다.
3. **디지털/AI가 아날로그의 한계를 보완하는 방향으로 수렴하고 있다** — DSP 기반 유무선 통신, MLSD/신경망 이퀄라이제이션, AI 기반 RF 역설계까지, "아날로그 회로를 더 정교하게 만드는" 접근에서 "디지털 연산으로 아날로그의 부족한 부분을 계산으로 메우는" 접근으로 무게중심이 이동하고 있다.

## 시험·복습 체크포인트

- Fractional-Spaced FFE가 자가상관행렬의 고유값 분석만으로 디지털 CDR 역할을 대신할 수 있는 이유는?
- Zoom ADC에서 Coarse 단에 CT-$\Delta\Sigma$를 쓰는 최신 구조가 기존(SAR coarse) 대비 얻는 이점은?
- Counter 기반 CTLE 적응이 Pulse Charging 기법과 다른 점, 그리고 하드웨어 효율이 좋은 이유는?
- MET CDR에서 SAR 방식이 기존 dithering 기반 방식보다 훨씬 빠르게 수렴하는 원리는?
- Chord Signaling에서 Walsh-Hadamard 변환이 ISI-Ratio=1을 달성하는 원리를 설명할 수 있는가?
- 대규모 뉴럴 인터페이스에서 "raw data 전송이 불가능해지는" 이유를 데이터 레이트 계산으로 설명할 수 있는가?
- AI 기반 RF 역설계에서 Diffusion 모델이 GA(유전 알고리즘) 대비 갖는 장점(multi-solution)은?
{% endraw %}

---

이전: [07. 클록 데이터 복원 (Clock and Data Recovery)](07-clock-and-data-recovery.md)
