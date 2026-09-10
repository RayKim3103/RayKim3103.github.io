---
layout: page
title: "07. 캐스코드와 전류 미러"
permalink: /studies/circuits/electric-circuits-1/07-cascode-and-current-mirrors/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/07%20%EC%BA%90%EC%8A%A4%EC%BD%94%EB%93%9C%EC%99%80%20%EC%A0%84%EB%A5%98%20%EB%AF%B8%EB%9F%AC.md) · 교재: Razavi Ch.9

{% raw %}
## 한눈에 보기

고이득 증폭기와 안정적 바이어싱을 위한 두 회로: **cascode**(출력저항을 키워 이득 ↑)와 **current mirror**(기준 전류를 복사해 bias 전류 생성).

```text
intrinsic gain 한계 → output resistance boost → cascode
→ current mirror → W/L 비로 전류 복사 → λ effect·PVT 대응 (cascode mirror)
```

---

## 1. Intrinsic Gain의 한계

CS 증폭기에서 부하가 이상적(무한대)이면 이득은 소자 고유값에 수렴:

$$
A_v \to -g_m r_o \qquad (\text{MOSFET: } \sim 20\text{–}100,\ \text{BJT: } V_A/V_T \sim 1000\text{s})
$$

실제 $$r_o$$는 유한. 더 큰 이득 → $$g_m$$ 또는 $$R_{out}$$을 키워야. $$g_m$$을 키우면 전류·면적이 커지므로, **$$R_{out}$$을 키우는 cascode**가 실용적.

## 2. Source Degeneration에 의한 출력저항 증가

MOSFET source에 저항 $$R_E$$:
$$
R_{out} = R_E + (1 + g_m R_E)\,r_o \approx g_m r_o R_E \quad (g_m R_E \gg 1)
$$

$$g_m R_E$$가 loop gain처럼 작용. **$$R_E$$를 단순 저항이 아니라 다른 트랜지스터의 $$r_o$$로 바꾸면** 출력저항이 크게 boost → cascode.

## 3. Cascode 구조

**common-source(아래) 위에 common-gate(위)**를 쌓음.
- 아래 소자 $$M_1$$: 입력 전압 → 전류
- 위 소자 $$M_2$$: $$M_1$$의 drain 전압 변화를 억눌러 출력저항을 키움

$$
R_{out} \approx r_{o1} + (1 + g_{m2}r_{o1})\,r_{o2} \;\xrightarrow{g_{m2}r_{o1}\gg1}\; g_{m2}\,r_{o1}\,r_{o2}
$$

$$
A_v \approx -g_{m1} R_{out} \approx -g_{m1}g_{m2}r_{o1}r_{o2} \sim -(g_m r_o)^2
$$

### 직관
cascode는 $$M_1$$의 drain 전압을 거의 **고정**한다 → drain 전압 변화 작음 → channel length modulation 영향 ↓ → $$M_1$$이 더 이상적인 전류원.

| Cascode 효과 | |
|---|---|
| 출력저항 | $$\times g_m r_o$$ 배 boost |
| 전압 이득 | $$\sim (g_m r_o)^2$$ |
| Miller 효과 | 감소 (대역폭 ↑) |
| 전압 headroom | **증가** (소자 2개가 saturation 유지해야) |
| 출력 swing | 감소 |

### Double cascode
소자를 더 쌓아 $$R_{out}$$을 $$\times(g_m r_o)^2$$까지. 대가: headroom 더 필요, bias 설계 난이도 ↑, swing 더 감소.

---

## 4. Current Mirror

### 목적
기준 전류 $$I_{REF}$$로 다른 가지에 동일/비례 전류 생성. 저항 분배 bias는 **PVT(공정·전압·온도) 변화에 민감** → current mirror는 **소자 matching**을 이용해 robust한 bias current.

### 기본 MOS current mirror
$$M_1$$(diode-connected, $$V_{GS}=V_{DS}$$)에 $$I_{REF}$$를 흘리면 그에 맞는 $$V_{GS}$$ 결정:
$$
V_{GS} = V_{th} + \sqrt{\frac{2I_{REF}}{\mu C_{ox}(W/L)_1}}
$$
이 $$V_{GS}$$를 $$M_2$$의 gate에 공유 (둘 다 saturation, 같은 공정·$$V_{th}$$):
$$
\boxed{\frac{I_{out}}{I_{REF}} = \frac{(W/L)_2}{(W/L)_1}}
$$

> 소자 크기를 1×, 2×, 3×로 만들면 $$I_{REF}$$의 1×, 2×, 3× 전류. (레이아웃은 unit transistor 병렬로 → matching ↑)

### Lambda effect 문제
$$
I_D = \frac12\mu C_{ox}\frac WL V_{OV}^2(1 + \lambda V_{DS})
$$
$$M_1$$과 $$M_2$$의 $$V_{DS}$$가 다르면($$V_{DS1}=V_{GS}$$ 고정, $$V_{DS2}$$는 부하 따라 변동) 전류가 정확히 안 맞음:
$$
\frac{I_{out}}{I_{REF}} = \frac{(W/L)_2}{(W/L)_1}\cdot\frac{1+\lambda V_{DS2}}{1+\lambda V_{DS1}}
$$

### 완화 방법
- **cascode** 사용 → 출력저항 ↑ → $$V_{DS2}$$ 변화의 영향 ↓
- source degeneration 저항 (전류에 부negative feedback, matching은 $$V_{GS}$$ 대신 $$I\cdot R$$로 결정 → 저항 matching이 소자 matching보다 좋음)
- **long channel** ($$\lambda \propto 1/L$$ 감소 → $$r_o$$ ↑)

### Bandgap reference
정확한 $$I_{REF}$$ 자체를 만들려면 기준이 안정해야 함. **Bandgap reference** = BJT $$V_{BE}$$의 음의 온도계수(−2 mV/°C)와 $$\Delta V_{BE}$$의 양의 온도계수(PTAT)를 가중합해 **온도·공정에 둔감한 ~1.2 V 기준**을 생성.

### Cascode current mirror
mirror의 출력단에 cascode 적용:
| 장점 | 단점 |
|---|---|
| 전류 복사 정확도 ↑ (출력저항 $$\sim g_m r_o^2$$) | 소자 더 필요 |
| λ 효과 감소 | 전압 headroom 더 필요 |

(Wilson mirror, Widlar mirror 등도 같은 목표의 변형: Wilson은 피드백으로 정확도, Widlar는 emitter/source 저항으로 매우 작은 전류.)

## 5. Current Mirror 해석 절차

1. diode-connected 기준 소자의 전류로 $$V_{GS}$$ 결정
2. 같은 $$V_{GS}$$가 걸리는 복사 소자 식별
3. 각 소자 $$(W/L)$$ 비 비교 → 전류비
4. 모든 소자 saturation 조건 확인
5. λ 고려 필요 시 $$r_o$$와 $$V_{DS}$$ 차이 반영

---

## 핵심 정리

- Intrinsic gain $$\approx g_m r_o$$ — 소자 고유 상한 (MOS ~수십, BJT ~수천).
- Degeneration: $$R_{out} \approx g_m r_o R_E$$. $$R_E$$를 트랜지스터 $$r_o$$로 바꾸면 = cascode.
- Cascode: $$R_{out} \approx g_m r_o^2$$, $$A_v \sim (g_m r_o)^2$$. 대가 = headroom, swing 감소.
- Current mirror: $$I_{out}/I_{REF} = (W/L)_2/(W/L)_1$$ (이상), 소자 matching 기반 → PVT robust.
- λ 효과가 복사 정확도를 떨어뜨림 → cascode mirror / degeneration / long channel로 완화.
- 정확한 $$I_{REF}$$는 bandgap reference로.

## 복습 질문

- intrinsic gain $$g_m r_o$$의 의미와, MOSFET에서 왜 작은가?
- cascode 출력저항 $$g_{m2}r_{o1}r_{o2}$$를 유도하고, "아래 소자 drain 전압을 고정한다"는 직관과 연결할 수 있나?
- 기본 current mirror에서 전류비가 $$(W/L)$$ 비인 이유, diode-connected 소자의 역할은?
- λ 효과가 mirror 정확도를 떨어뜨리는 메커니즘과, cascode mirror가 이를 개선하는 이유는?
- cascode의 이득 이점과 headroom·swing 손실의 trade-off는?
{% endraw %}

---

이전: [06. MOSFET 증폭기](06-mosfet-amplifiers.md) · 다음: [08. CMOS 인버터](08-cmos-inverter.md)
