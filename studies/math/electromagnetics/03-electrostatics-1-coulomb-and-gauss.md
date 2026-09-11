---
layout: page
title: "03. 정전계 I - 쿨롱 법칙과 가우스 법칙"
permalink: /studies/math/electromagnetics/03-electrostatics-1-coulomb-and-gauss/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electromagnetics/lecture_notes/04%20%EC%A0%95%EC%A0%84%EA%B3%84%20I%20-%20%EC%BF%A8%EB%A1%B1%20%EB%B2%95%EC%B9%99%EA%B3%BC%20%EA%B0%80%EC%9A%B0%EC%8A%A4%20%EB%B2%95%EC%B9%99.md)

{% raw %}
## 핵심 요약

정전계는 정지한 전하가 만드는 시간 불변 전기장을 다룬다. 이 장의 출발점은 쿨롱 법칙과 중첩 원리이다. 점전하에서 시작해 연속 전하분포의 전기장을 적분으로 구하고, 대칭성이 높은 문제에서는 가우스 법칙을 사용해 훨씬 간단히 전기장을 구한다.

## 정전계의 조건

- 전하가 정지해 있다.
- 전기장이 시간에 따라 변하지 않는다.
- 자기장과 유도 전기장은 고려하지 않는다.
- 전기장은 보존장이므로 닫힌 경로 선적분이 0이다.

$$
\oint \mathbf{E}\cdot d\boldsymbol{l} = 0,\qquad \nabla\times\mathbf{E}=0
$$

## 쿨롱 법칙

점전하 사이의 힘은 거리 제곱에 반비례하고 두 전하의 곱에 비례한다.

$$
\mathbf{F} = \frac{1}{4\pi\varepsilon_0}\frac{q_1q_2}{R^2}\mathbf{a}_R
$$

전기장은 시험전하가 받는 힘을 단위 전하당 힘으로 나눈 것이다.

$$
\mathbf{E} = \mathbf{F}/q
$$

점전하 $$Q$$ 가 원점에 있으면 전기장은 방사형 대칭을 가진다.

$$
\mathbf{E} = \frac{1}{4\pi\varepsilon_0}\frac{Q}{r^2}\mathbf{a}_r
$$

## 중첩 원리

여러 전하가 있을 때 전체 전기장은 각 전하가 만드는 전기장의 벡터합이다.

$$
\mathbf{E}_{total} = \sum_i \mathbf{E}_i
$$

연속 전하분포에서는 $$dq$$ 가 만드는 $$d\mathbf{E}$$ 를 적분한다.

$$
dq=\rho_v\,dV,\qquad dq=\rho_s\,dS,\qquad dq=\rho_l\,dl
$$

부피 전하, 표면 전하, 선 전하 중 어떤 모델이 적절한지 먼저 골라야 한다.

## 전기 쌍극자

전기 쌍극자는 같은 크기와 반대 부호의 전하가 작은 거리만큼 떨어진 구조이다. 멀리서 보면 단일 점전하보다 빠르게 장이 감소한다($$\propto 1/r^3$$, 점전하는 $$1/r^2$$). 쌍극자 모멘트는 보통

$$
\mathbf{p} = q\mathbf{d}
$$

로 정의하며, 방향은 음전하에서 양전하 쪽이다. 유전체 분극, 안테나, 분자 모델에서 쌍극자 개념이 반복된다.

## 전기 플럭스와 가우스 법칙

전기 플럭스는 닫힌 면을 통과하는 전기 플럭스 밀도의 법선 성분 합이다.

$$
\oint_S \mathbf{D}\cdot d\mathbf{S} = Q_{enc}
$$

자유공간에서는 $$\mathbf{D}=\varepsilon_0\mathbf{E}$$ 이므로 다음과 같이 쓸 수 있다.

$$
\oint_S \mathbf{E}\cdot d\mathbf{S} = Q_{enc}/\varepsilon_0
$$

미분형은 발산 정리로 얻는다.

$$
\nabla\cdot\mathbf{D} = \rho
$$

## 가우스 법칙을 쓰기 좋은 경우

가우스 법칙은 항상 성립하지만, 전기장을 쉽게 구할 수 있는 경우는 대칭성이 강할 때이다.

- 구대칭: 구형 가우스 면
- 원통대칭: 원통형 가우스 면
- 무한 평면: pillbox 형태의 가우스 면

핵심은 가우스 면 위에서 $$\mathbf{E}$$ 의 크기가 일정하고 방향이 면의 법선과 평행 또는 수직이 되도록 잡는 것이다.

## 대표 예시

### 무한 선전하 — 전체 유도 worked example

선전하밀도 $$\rho_l$$ [C/m]인 무한히 긴 직선이 $$z$$ 축에 있다고 하자. 원통 대칭이므로 반지름 $$r$$, 길이 $$L$$ 인 원통 가우스 면을 잡으면 $$\mathbf{E}=E_r\mathbf{a}_r$$(옆면에서만 플럭스 통과, 위아래 뚜껑은 $$\mathbf{E}\perp d\mathbf{S}$$ 라 기여 없음):

$$
\oint_S \mathbf{E}\cdot d\mathbf{S} = E_r \cdot (2\pi r L) = \frac{Q_{enc}}{\varepsilon_0} = \frac{\rho_l L}{\varepsilon_0}
\quad\Longrightarrow\quad
\mathbf{E} = \frac{\rho_l}{2\pi\varepsilon_0 r}\mathbf{a}_r
$$

**숫자 예**: $$\rho_l = 10\text{ nC/m}$$, $$r=0.1\text{ m}$$ 이면 $$E = \dfrac{10\times10^{-9}}{2\pi(8.85\times10^{-12})(0.1)} \approx 1798\text{ V/m}$$. 점전하의 $$1/r^2$$ 와 달리 선전하는 $$1/r$$ 로 느리게 감소한다 — 같은 총 전하량이라도 분포 차원이 낮을수록(점 < 선 < 면) 장이 더 느리게 감소한다는 일반적 패턴을 보여준다.

### 균일하게 대전된 구껍질

- $$r>R$$: 외부에서는 점전하처럼 보인다. $$E=\dfrac{Q}{4\pi\varepsilon_0 r^2}$$
- $$r<R$$: 내부 전기장은 0이다.

구껍질 내부에서 전기장이 0이 되는 결과는 $$1/r^2$$ 법칙과 구대칭이 결합된 결과이다.

### 무한 평면 전하

$$
\mathbf{E} = \frac{\rho_s}{2\varepsilon_0}\mathbf{a}_n
$$

평면 양쪽으로 같은 크기의 전기장이 생긴다. 전기장은 평면에 수직이고, 무한 평면이라는 이상화 때문에 위치에 의존하지 않는다.

### 평행판

두 무한 평행판 사이에서는 장이 거의 균일하다($$E=\rho_s/\varepsilon_0$$, 두 판의 장이 사이에서는 더해지고 바깥에서는 상쇄). 실제 유한한 판에서는 가장자리에서 fringing field가 생긴다.

## 전위와 전기장

정전계는 보존장이므로 전위를 정의할 수 있다.

$$
V(P) = W/q,\qquad \mathbf{E}=-\nabla V,\qquad V_{ab} = -\int_a^b \mathbf{E}\cdot d\boldsymbol{l}
$$

점전하의 전위는 보통 무한대를 기준점으로 잡는다($$V=\dfrac{Q}{4\pi\varepsilon_0 r}$$). 그러나 무한 선전하처럼 전위가 무한대에서 발산하는 구조에서는 두 점 사이 전위차를 직접 계산해야 한다. 예를 들어 위 무한 선전하의 경우
$$
V_{ab} = -\int_a^b E_r\,dr = -\frac{\rho_l}{2\pi\varepsilon_0}\ln\frac{b}{a}
$$
로, 기준점을 무한대로 두면 발산하므로 반드시 유한한 기준 반지름을 잡아야 한다.

## 도체의 정전 평형

정전 평형 상태의 금속 도체에서는 내부 전기장이 0이다. 내부에 전기장이 남아 있으면 자유전하가 계속 이동하므로 정전 상태가 될 수 없다.

결과적으로 다음 성질이 나온다.

- 도체 내부는 등전위이다.
- 과잉 전하는 표면에 존재한다.
- 도체 표면에서 접선 전기장은 0이다.
- 빈 공동 내부에 전하가 없으면 공동 내부 전기장도 0이다.
- 곡률이 큰 곳에서 표면전하밀도와 법선 전기장이 커질 수 있다.

## 포아송 방정식과 라플라스 방정식

전하분포보다 경계 전위가 주어진 문제에서는 전위를 직접 구한다.

$$
\nabla^2 V = -\rho/\varepsilon,\qquad \nabla^2V=0\ (\rho=0)
$$

도체 경계가 등전위로 주어지고 영역 내부가 전하가 없는 경우에는 [라플라스 방정식과 경계조건](02-vector-calculus-and-field-theory.md)을 푸는 문제가 된다.

## 연결 노트

- [벡터 미적분과 장 이론](02-vector-calculus-and-field-theory.md)
- [정전계 II - 유전체와 커패시턴스](04-electrostatics-2-dielectrics-and-capacitance.md)
- [정상 전류와 저항](05-steady-current-and-resistance.md)

## 복습 질문

- 쿨롱 법칙에서 전기장 정의 $$\mathbf{E}=\mathbf{F}/q$$ 로 이어지는 논리를 설명할 수 있는가?
- 무한 선전하에 대해 가우스 원통면을 잡고 $$E=\rho_l/2\pi\varepsilon_0 r$$ 를 직접 유도할 수 있는가?
- 점전하($$1/r^2$$), 선전하($$1/r$$), 평면전하(상수)의 감소율 차이가 왜 생기는지 차원 논증으로 설명할 수 있는가?
- 무한 선전하의 전위가 왜 무한대를 기준점으로 잡을 수 없는지 설명할 수 있는가?
- 도체 정전 평형의 다섯 가지 성질을 내부 $$E=0$$ 으로부터 유도할 수 있는가?

{% endraw %}

---

이전: [02. 벡터 미적분과 장 이론](02-vector-calculus-and-field-theory.md) · 다음: [04. 정전계 II - 유전체와 커패시턴스](04-electrostatics-2-dielectrics-and-capacitance.md)
