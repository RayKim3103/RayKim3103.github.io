---
layout: page
title: "06. MOSFET 증폭기"
permalink: /studies/circuits/electric-circuits-1/06-mosfet-amplifiers/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/06%20MOSFET%20%EC%A6%9D%ED%8F%AD%EA%B8%B0.md) · 교재: Razavi Ch.7

{% raw %}
## 한눈에 보기

MOSFET 증폭기. DC bias로 **saturation** 동작점을 잡고 → 소신호 모델 → common-source, source degeneration, 능동 부하, 다단.

```text
DC bias → saturation check → gm, ro
→ small signal equivalent → common-source gain
→ source degeneration → active load → multi-stage
```

---

## 1. Common-Source 기본

NMOS CS: gate 입력, drain 출력, drain에 $$R_D$$. **위상 반전**.

$$
v_{out} = -i_d R_D, \qquad i_d = g_m v_{gs} \;\Rightarrow\; A_v = -g_m R_D
$$

$$r_o$$ 고려:
$$
A_v = -g_m (R_D \parallel r_o)
$$

$$R_D$$ 대신 **이상적 전류원 부하**면 $$A_v = -g_m r_o$$ = **intrinsic gain** (그 소자로 낼 수 있는 최대). 부호 −: $$v_{gs}\uparrow \to I_D\uparrow \to R_D$$ 강하 ↑ $$\to V_D\downarrow$$.

- $$R_{in} = \infty$$ (gate, bias 저항 병렬 시 그 값)
- $$R_{out} = R_D \parallel r_o$$

## 2. DC Bias

BJT처럼 먼저 DC 동작점 → saturation 확인 ($$V_{DS} > V_{OV}$$).

### Resistive divider bias
$$
V_G = \frac{R_2}{R_1 + R_2}V_{DD}
$$
gate DC current ≈ 0 → **BJT보다 분압 설계 단순** ($$I_X \gg I_B$$ 조건 불필요, 매우 큰 저항 사용 가능).
그다음 $$V_{GS} = V_G - I_D R_S$$, square-law 식과 연립해 $$I_D$$.

### Self bias (drain–gate 연결)
음의 피드백 → 동작점 안정. $$V_G = V_D$$ 이면 $$V_{GS} = V_{DS}$$ →
$$
V_{DS} = V_{GS} > V_{GS} - V_{th} = V_{OV}
$$
→ **saturation을 자동 만족** (diode-connected 형태).

### AC 해석 규칙
- coupling capacitor → AC short
- $$V_{DD}$$ → AC ground
- gate DC current = 0
- MOSFET → $$g_m v_{gs}$$ 전류원 ∥ $$r_o$$

## 3. Gate Capacitance와 주파수

MOS = conductor–insulator–conductor → **커패시터** ($$C_{gs}, C_{gd}$$).
$$
Z_{C_{gs}} = \frac{1}{sC_{gs}}
$$
저주파: 임피던스 커서 입력 전달에 큰 영향 X; 고주파: 작아져 입력 감쇠·대역 제한 ($$C_{gd}$$는 Miller로 증폭). 강의 노트는 "주파수가 매우 낮지 않으면 입력 커패시턴스가 거의 short처럼 작동"하는 단순화된 상황을 다룬다.

---

## 4. Source Degeneration이 있는 CS

source에 $$R_S$$ 삽입:
$$
A_v \approx -\frac{R_D}{1/g_m + R_S} \qquad (r_o \to \infty)
$$
$$r_o$$ 포함 시 출력저항은 $$R_D \parallel [R_S + (1+g_m R_S)r_o]$$로 커진다.

BJT emitter degeneration과 동일한 역할:
- 이득 감소 ($$R_S \gg 1/g_m$$이면 $$\approx -R_D/R_S$$, $$g_m$$에 둔감)
- 선형성 증가
- 동작점 안정화
- 입력 대비 출력 전류 변화 완화 (effective $$G_m = g_m/(1+g_m R_S)$$)

## 5. 부하 종류별 CS 이득

$$
A_v = -g_m R_{out}
$$

| drain 부하 | $$R_{out}$$ | 특징 |
|---|---|---|
| 저항 $$R_D$$ | $$R_D \parallel r_o$$ | headroom 소모, 이득 제한 |
| **이상 전류원** | $$r_o$$ | $$A_v = -g_m r_o$$ (intrinsic gain) |
| **전류원 부하** (PMOS) | $$r_{o,n} \parallel r_{o,p}$$ | 실제 능동 부하, 큰 이득 |
| **diode-connected MOS** | $$\approx 1/g_{m,load}$$ | $$A_v \approx -g_{m1}/g_{m2} = -\sqrt{(W/L)_1/(W/L)_2}$$, 이득은 작지만 매우 선형·안정 |

> current source load에서 $$\lambda = 0$$이면 $$r_o = \infty$$ → 이득 무한대(이상). 실제 $$\lambda \ne 0$$이라 유한.

## 6. BJT ↔ MOSFET 토폴로지 대응

| BJT | MOSFET | 기능 |
|---|---|---|
| Common-emitter | **Common-source** | 전압 증폭, 위상 반전 |
| Common-base | **Common-gate** | 낮은 $$R_{in}$$($$1/g_m$$), 높은 $$R_{out}$$, 고주파 |
| Common-collector | **Common-drain (source follower)** | $$A_v \lesssim 1$$, voltage buffer |

**Source follower**: $$A_v = \dfrac{g_m(R_L\parallel r_o)}{1 + g_m(R_L\parallel r_o)} \lesssim 1$$, $$R_{out} \approx 1/g_m$$.

## 7. 다단 MOS 증폭기

각 단 이득 곱: $$A_v = A_{v1}A_{v2}\cdots$$. 이전 단 출력저항이 다음 단의 Thevenin 구동저항. MOSFET는 $$R_{in} = \infty$$라 CS단 사이 loading이 없어 계산이 BJT보다 단순 (CG·CD 단이 끼면 loading 발생).

### 예제형 이득 절차
1. 각 MOSFET saturation 확인
2. 각 소자 $$g_m$$, $$r_o$$ 계산
3. 출력 노드에서 보이는 등가저항 $$R_{out}$$
4. $$A_v = -g_m R_{out}$$ 또는 degeneration 식
5. 다단이면 단별 곱

---

## 핵심 정리

- MOSFET 증폭기는 **saturation**에서 사용.
- CS 이득 = $$-g_m(R_D \parallel r_o)$$; 이상 전류원 부하면 $$-g_m r_o$$ (intrinsic gain).
- Source degeneration: $$A_v \approx -R_D/(1/g_m + R_S)$$, 이득↓ 선형성·안정성·$$R_{out}$$↑, $$G_m = g_m/(1+g_m R_S)$$.
- 능동 부하(current source, diode-connected)로 $$R_{out}$$을 키워 이득 확보.
- CS/CG/CD ↔ CE/CB/CC 대응. source follower $$A_v \lesssim 1$$, $$R_{out} \approx 1/g_m$$.
- 다단: 단별 이득 곱, MOS는 gate $$R_{in}=\infty$$라 CS-CS 간 loading 없음.

## 복습 질문

- CS 이득 부호가 −인 이유, $$-g_m(R_D\parallel r_o)$$를 소신호 등가회로에서 유도할 수 있나?
- self bias(drain–gate 단락)가 saturation을 자동 보장하는 이유는?
- source degeneration이 effective $$G_m$$과 이득·$$R_{out}$$에 주는 영향은?
- diode-connected MOS 부하의 소신호 저항이 $$1/g_m$$이고, CS 이득이 $$-\sqrt{(W/L)_1/(W/L)_2}$$인 것을 보일 수 있나?
- source follower의 $$A_v$$와 $$R_{out}$$ 식, buffer로 쓰는 이유는?
{% endraw %}

---

이전: [05. MOSFET 기본 동작](05-mosfet-operation.md) · 다음: [07. 캐스코드와 전류 미러](07-cascode-and-current-mirrors.md)
