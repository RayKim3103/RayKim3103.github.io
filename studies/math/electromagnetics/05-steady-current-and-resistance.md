---
layout: page
title: "05. 정상 전류와 저항"
permalink: /studies/math/electromagnetics/05-steady-current-and-resistance/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electromagnetics/lecture_notes/06%20%EC%A0%95%EC%83%81%20%EC%A0%84%EB%A5%98%EC%99%80%20%EC%A0%80%ED%95%AD.md)

{% raw %}
## 핵심 요약

정상 전류장은 움직이는 자유전하가 만드는 전류밀도 $$\mathbf{J}$$ 를 다룬다. 미시적으로는 전자의 열운동과 충돌, 외부 전기장에 의한 drift velocity가 옴의 법칙으로 이어진다. 거시적으로는 연속 방정식, KCL, KVL, 저항, 전력 손실, 도체와 유전체의 이중성이 핵심이다.

## 전류의 종류

강의는 전류를 다음처럼 구분한다.

- Conduction current: 금속이나 반도체에서 전자와 정공의 drift 운동
- Convection current: 진공 중 전자 빔처럼 매질 없이 움직이는 전하
- Electrolytic current: 전해질에서 양이온과 음이온의 이동
- Displacement current: 시간 변화 전기 플럭스에 대응하는 전류 성분

정상 전류 장에서는 주로 conduction current를 다룬다.

## 전류밀도 J

전류밀도는 단위 면적당 흐르는 전류이다.

$$
I = \int_S \mathbf{J}\cdot d\mathbf{S}
$$

1차원 도선 전류는 $$I$$[A], 3차원 부피 전류는 $$\mathbf{J}$$[A/m²], 2차원 표면 전류는 $$\mathbf{J}_s$$[A/m]로 구분한다.

전자 농도 $$n_c$$, 전하량 $$q$$, 평균 drift velocity $$\mathbf{v}_d$$ 를 쓰면

$$
\mathbf{J} = n_c q \mathbf{v}_d
$$

로 표현된다. 전자의 열속도는 매우 크지만 무작위라 평균 전류를 만들지 않고, 외부 전기장이 만든 작은 drift 성분이 실제 전류를 만든다.

## 점 형태의 옴의 법칙

충돌 때문에 전자는 계속 가속되지 않고 평균 drift velocity가 전기장에 비례한다. 이 관계가 거시적으로 전도도 $$\sigma$$ 를 만든다.

$$
\mathbf{J} = \sigma\mathbf{E},\qquad \mathbf{E} = \rho_c\mathbf{J}
$$

여기서 $$\sigma$$ 는 전도도이고 $$\rho_c=1/\sigma$$ 는 비저항이다. 금속은 $$\sigma$$ 가 크고, 고무나 석영은 매우 작다. 반도체의 전도도는 온도와 carrier 농도에 크게 의존한다.

## 저항

도체의 두 단자 전위차와 전류의 비를 저항이라 한다.

$$
R = V/I
$$

균일한 단면적 $$A$$, 길이 $$l$$, 전도도 $$\sigma$$ 를 가진 도체에서는

$$
R = \frac{l}{\sigma A}
$$

일반 구조에서는 다음 절차로 구한다.

1. 두 단자 사이 전위차 $$V$$ 를 가정한다.
2. 도체 내부 전기장 $$\mathbf{E}$$ 를 구한다.
3. $$\mathbf{J}=\sigma\mathbf{E}$$ 로 전류밀도를 구한다.
4. $$I=\int\mathbf{J}\cdot d\mathbf{S}$$ 로 전체 전류를 구한다.
5. $$R=V/I$$ 를 계산한다.

### Worked example — 동축 케이블의 (누설) 저항

[04장](04-electrostatics-2-dielectrics-and-capacitance.md)과 같은 기하(내경 $$a$$, 외경 $$b$$, 길이 $$L$$, 사이 매질 전도도 $$\sigma$$)에서, 내부 도체에서 외부로 방사 방향으로 흐르는 누설 전류 $$I$$ 를 가정하면 원통 대칭이므로:

$$
J_r = \frac{I}{2\pi r L},\qquad E_r = \frac{J_r}{\sigma} = \frac{I}{2\pi\sigma r L}
$$
$$
V = \int_a^b E_r\,dr = \frac{I}{2\pi\sigma L}\ln\frac{b}{a}
\quad\Longrightarrow\quad
R = \frac{V}{I} = \frac{\ln(b/a)}{2\pi\sigma L}
$$

**숫자 예**: 04장과 같은 치수($$a=1$$mm, $$b=4$$mm, $$L=1$$m)에 절연체의 미세한 누설 전도도 $$\sigma=10^{-15}\text{ S/m}$$ 를 대입하면 $$R = \dfrac{\ln4}{2\pi(10^{-15})(1)} \approx 2.2\times10^{14}\,\Omega$$. 04장의 $$C\approx92.3\text{ pF}$$ 와 곱하면 $$RC \approx 2.03\times10^{4}\text{ s}$$ 이고, 이는 아래 $$RC=\varepsilon/\sigma = (2.3\times8.85\times10^{-12})/10^{-15}\approx2.04\times10^{4}\text{ s}$$ 와 일치한다 — **같은 기하에서 R과 C를 각각 구해 곱하면 항상 매질의 $$\varepsilon/\sigma$$ 만 남는다**는 이중성이 숫자로 확인된다.

## 연속 방정식

전하 보존은 연속 방정식으로 표현된다.

$$
\nabla\cdot\mathbf{J} = -\frac{\partial\rho}{\partial t}
$$

정상 전류에서는 시간에 따른 전하 축적이 없으므로

$$
\nabla\cdot\mathbf{J} = 0
$$

이다. 즉 정상 전류선은 내부에서 시작하거나 끝나지 않는다. 회로 이론의 KCL은 이 식을 접합부에 적용한 결과이다.

## KCL과 KVL

정상 상태에서 임의의 닫힌 면을 통과하는 총 전류는 0이다.

$$
\sum_j I_j = 0
$$

이것이 KCL이다. 한편 정전기장만으로는 닫힌 회로에 지속적인 전류를 유지할 수 없다. 정전기장은 보존장이므로

$$
\oint \mathbf{E}\cdot d\boldsymbol{l} = 0
$$

이고, 저항에서 에너지를 잃은 전하를 다시 올려줄 비보존 에너지 source가 필요하다. 배터리나 발전기가 제공하는 기전력 $$V_{emf}$$ 를 포함하면 회로의 KVL 형태가 나온다.

$$
V_{emf} = IR
$$

## 전하 완화 시간

도체 내부에 부피 전하가 생기면 빠르게 재분포한다. 완화 시간은

$$
\tau = \varepsilon/\sigma
$$

로 주어지며, 물질이 도체처럼 동작하는지 절연체처럼 동작하는지 판단하는 척도이다. 금속에서는 극도로 짧고(예: 구리 $$\tau\sim10^{-19}$$s), 석영 같은 절연체에서는 매우 길다. 위 동축 케이블 예제의 $$RC\approx2\times10^4$$s는 사실 이 $$\tau=\varepsilon/\sigma$$ 그 자체다 — 우연이 아니라 정의상 같은 물리량이다.

## 전력 손실

전기장이 carrier에 일을 하고, 그 에너지는 충돌을 통해 열로 바뀐다. 점 형태의 Joule 법칙은

$$
p = \mathbf{E}\cdot\mathbf{J},\qquad P = \int_V \mathbf{E}\cdot\mathbf{J}\,dV
$$

이다. 균일한 도체에서는 익숙한 회로식과 연결된다.

$$
P = VI = I^2R = V^2/R
$$

## 경계조건

정상 전류 경계에서는 전류밀도의 법선 성분이 연속이다.

$$
J_{1n} = J_{2n}
$$

전기장의 접선 성분은 정전계 관계에서 연속이고, $$\mathbf{J}=\sigma\mathbf{E}$$ 이므로 전도도가 다른 두 매질에서는 $$\mathbf{J}$$ 의 접선 성분 비가 전도도 비를 따른다. 완전 절연체와 접한 도체 표면에서는 법선 전류가 0이 되어 전류가 표면을 따라 흐른다.

## J와 D의 이중성

정상 전류 문제와 유전체 커패시턴스 문제는 수학적 구조가 비슷하다.

| 도전 매질 | 유전체 |
|---|---|
| $$\mathbf{J}=\sigma\mathbf{E}$$ | $$\mathbf{D}=\varepsilon\mathbf{E}$$ |
| $$\nabla\cdot\mathbf{J}=0$$ | $$\nabla\cdot\mathbf{D}=\rho_{free}$$ |
| $$R=V/I$$ | $$C=Q/V$$ |

같은 기하 구조에서 저항을 알면 커패시턴스를 유추할 수 있는 경우가 있다. 대표적으로 $$RC=\varepsilon/\sigma$$ 형태의 관계가 등장하며, 위 동축 케이블 worked example이 이를 정확히 재현한다.

## 연결 노트

- [정전계 II - 유전체와 커패시턴스](04-electrostatics-2-dielectrics-and-capacitance.md)
- [정자계 I - 비오-사바르와 앙페르 법칙](06-magnetostatics-1-biot-savart-and-ampere.md)
- [시간 변화장과 맥스웰 방정식](08-time-varying-fields-and-maxwell.md)

## 복습 질문

- $$\mathbf{J}=n_cq\mathbf{v}_d$$ 에서 열속도가 아니라 drift velocity만 순 전류를 만드는 이유는?
- 동축 케이블 저항 $$R=\ln(b/a)/(2\pi\sigma L)$$ 를 저항 계산 5단계로 직접 유도할 수 있는가?
- $$RC=\varepsilon/\sigma$$ 가 왜 전하 완화 시간 $$\tau=\varepsilon/\sigma$$ 와 같은 식인지 설명할 수 있는가?
- 연속 방정식 $$\nabla\cdot\mathbf{J}=-\partial\rho/\partial t$$ 에서 정상 전류 조건 $$\nabla\cdot\mathbf{J}=0$$ 이 KCL로 이어지는 논리는?
- $$\mathbf{J}$$-$$\mathbf{D}$$ 이중성 표에서 대응되는 세 쌍을 설명할 수 있는가?

{% endraw %}

---

이전: [04. 정전계 II - 유전체와 커패시턴스](04-electrostatics-2-dielectrics-and-capacitance.md) · 다음: [06. 정자계 I - 비오-사바르와 앙페르 법칙](06-magnetostatics-1-biot-savart-and-ampere.md)
