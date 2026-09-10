---
layout: page
title: "02. 다이오드 응용"
permalink: /studies/circuits/electric-circuits-1/02-diode-applications/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/02%20%EB%8B%A4%EC%9D%B4%EC%98%A4%EB%93%9C%20%EC%9D%91%EC%9A%A9.md) · 교재: Razavi Ch.3

{% raw %}
## 한눈에 보기

다이오드 회로 응용. 핵심은 **모델을 정하고 → on/off를 가정하고 → 조각별(piecewise)로 입출력 관계를 해석하고 → 가정 검증**.

```text
지수 모델 → constant voltage model → ideal diode model
→ clipping/limiting → half-wave rectifier → full-wave rectifier → capacitor smoothing
```

---

## 1. 다이오드 전류식 복습

$$
I = I_S(e^{V_D/V_T} - 1) \;\xrightarrow{V_D \gg V_T}\; I \approx I_S e^{V_D/V_T}
$$

회로 응용에서는 이 지수식 대신 근사 모델을 쓴다.

| 모델 | 켜짐 | 꺼짐 |
|---|---|---|
| **Ideal diode** | 단락, $$V_D = 0$$ | 개방, $$I_D = 0$$ |
| **Constant-voltage** | $$V_D = V_{D,on}$$ (≈0.7~0.8 V) | $$I_D = 0$$ |

## 2. 다이오드 회로 해석 절차

1. 다이오드가 **켜졌다고 가정**.
2. 모델에 맞게 단락(ideal) 또는 전압원($$V_{D,on}$$)으로 치환.
3. 회로 방정식 → 전류 방향·전압 조건 확인.
4. 순방향 전류($$I_D > 0$$)면 가정 채택.
5. 아니면 **꺼짐**(개방)으로 다시 해석, 이때 $$V_D < V_{D,on}$$인지 확인.

> 다이오드 여러 개면 각 조합($$2^n$$)을 시험하되, 물리적 직관으로 후보를 줄인다. 회로는 **piecewise linear**로 본다.

---

## 3. 직렬 다이오드 (반주기 통과)

입력–직렬 다이오드–출력저항 $$R$$ 구조. 양의 부분만 통과.

**Ideal:**
$$
V_{out} = \begin{cases} V_{in}, & V_{in} > 0 \\ 0, & V_{in} \le 0 \end{cases}
$$

**Constant-voltage:**
$$
V_{out} = \begin{cases} V_{in} - V_{D,on}, & V_{in} > V_{D,on} \\ 0, & V_{in} \le V_{D,on} \end{cases}
$$

전달특성 그래프: 임계점이 ideal은 0, CVM은 $$V_{D,on}$$. 켜진 뒤 기울기는 1 (분압 없으면).

## 4. Shunt Clipping (병렬 클리퍼 / limiter)

입력–직렬 $$R_1$$–출력노드, 출력노드에서 다이오드가 접지로. 출력이 특정 전압 이상 못 올라감:

$$
V_{out} \approx V_{D,on} \quad (\text{다이오드 on일 때})
$$

입력이 더 커져도 초과분은 $$R_1$$에 걸린다. 다이오드에 배터리 $$V_B$$를 직렬로 넣으면 클리핑 레벨이 $$V_B + V_{D,on}$$으로 이동. 위·아래 다이오드 쌍이면 양방향 limiter.

### 저항 분배가 포함된 클리퍼
다이오드 **off** 구간: 단순 분압기
$$
V_{out} = \frac{R_2}{R_1 + R_2} V_{in}
$$
다이오드 **on** 이후: 출력이 $$V_{D,on}$$ 근처로 제한되거나, 회로에 따라 기울기가 바뀜.

### piecewise transfer curve 정리
- off: 저항망만 → 한 기울기
- on: 다이오드 전압 조건 추가 → 다른 기울기
- 꺾이는 점: ideal은 0 V, CVM은 $$V_{D,on}$$

→ **시간 파형과 전달특성 그래프를 함께** 그리면 실수 감소.

---

## 5. 정류기 (Rectifier)

AC → DC 근사. 음의 반주기를 제거/반전하고 커패시터로 리플을 줄여 DC 전원처럼 사용.

### 반파 정류기 (half-wave)
**Ideal:** 양의 반주기 $$V_{out} = V_{in}$$, 음의 반주기 $$V_{out} = 0$$.
**CVM:** 양의 반주기도 $$V_{out} = V_{in} - V_{D,on}$$.
- PIV(peak inverse voltage): 다이오드가 견뎌야 할 역전압 ≈ $$V_{p}$$ (필터 없을 때), 필터 있으면 $$\approx 2V_p$$.

### 커패시터 필터 (smoothing)
정류 출력에 커패시터 $$C$$를 부하 $$R_L$$과 병렬로:

- 입력 상승 → 다이오드 on → $$C$$ 충전 (피크까지)
- 입력 하강 → 다이오드 off → $$C$$가 $$R_L$$로 방전
- $$C$$ 크면 방전 느림 → **리플 작음**

리플 근사 (반파, $$RC \gg T$$):
$$
\Delta V_{ripple} \approx \frac{V_p}{f R_L C} = \frac{I_L}{f\,C}
$$
전파는 방전 시간이 반이라 $$\Delta V \approx I_L/(2fC)$$. 리플이 작을수록 DC에 가깝지만 다이오드 도통각이 좁아져 **첨두 충전전류**가 커진다.

### 전파 정류기 (full-wave / bridge)
양·음 반주기를 모두 양의 출력으로.
- **브리지**: 입력 극성에 따라 대각선 2개 다이오드 on, 나머지 2개 off.

**Ideal:** $$V_{out} = |V_{in}|$$
**CVM:** 도통 경로에 다이오드 2개 직렬 →
$$
V_{out} \approx |V_{in}| - 2V_{D,on}
$$
- center-tap 변압기 방식은 도통 경로에 다이오드 1개 → $$|V_{in}| - V_{D,on}$$, 대신 변압기 2배.
- 장점: 리플 주파수 2$$f$$ → 필터 부담 감소, 변압기 이용률 높음.

---

## 6. Zener 전압 레귤레이터 (참고)

역방향 breakdown 영역의 Zener 다이오드를 부하와 병렬로 두면 출력 전압이 $$V_Z$$로 고정. 직렬저항 $$R_s$$가 여분 전류를 흘림.
$$
I_{R_s} = \frac{V_{in} - V_Z}{R_s} = I_Z + I_L
$$
$$V_{in}$$이나 $$I_L$$이 변해도 $$V_Z \approx$$ 일정. line/load regulation은 Zener의 동저항 $$r_z$$로 결정.

## 7. 자주 하는 실수

- 다이오드 방향 ↔ 전류 방향 혼동
- ideal / CVM 임계전압을 섞어 씀
- "켜진 뒤 출력 = 입력"으로 착각 (분압·직렬강하 무시)
- 커패시터 필터에서 충전 구간 / 방전 구간 미구분
- 전파 정류기에서 도통 다이오드 **개수**(1 vs 2) 빠뜨려 $$V_{D,on}$$ 항 틀림

---

## 핵심 정리

- 다이오드 회로 = **on/off 가정 → 검증**, piecewise linear 해석.
- Ideal(정성) vs CVM($$V_{D,on}$$ 반영, 표준).
- 클리퍼/limiter는 출력을 일정 레벨로 제한. 전달특성은 꺾인 직선.
- 반파: 한쪽 반주기 / 전파: 양쪽 반주기를 같은 극성으로 (bridge는 $$-2V_{D,on}$$).
- 커패시터 필터 리플 $$\Delta V \approx I_L/(fC)$$ (전파는 $$/2fC$$); 작을수록 첨두 충전전류 ↑.

## 복습 질문

- ideal과 constant-voltage 모델로 같은 반파 정류기를 풀면 출력이 어떻게 다른가?
- 다이오드 2개 이상인 회로에서 on/off 조합을 어떻게 좁히고 검증하는가?
- shunt clipper의 전달특성 곡선을 $$R_1, R_2, V_{D,on}$$로 그릴 수 있는가?
- 브리지 전파 정류기 도통 경로에 다이오드가 왜 2개인가? center-tap 방식과의 차이는?
- 커패시터를 키우면 리플과 첨두 충전전류는 각각 어떻게 되는가?
{% endraw %}

---

이전: [01. 반도체와 PN 접합](01-semiconductors-and-pn-junction.md) · 다음: [03. BJT 기본 동작](03-bjt-operation.md)
