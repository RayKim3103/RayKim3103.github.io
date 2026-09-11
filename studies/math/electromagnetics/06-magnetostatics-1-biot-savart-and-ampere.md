---
layout: page
title: "06. 정자계 I - 비오-사바르와 앙페르 법칙"
permalink: /studies/math/electromagnetics/06-magnetostatics-1-biot-savart-and-ampere/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electromagnetics/lecture_notes/07%20%EC%A0%95%EC%9E%90%EA%B3%84%20I%20-%20%EB%B9%84%EC%98%A4-%EC%82%AC%EB%B0%94%EB%A5%B4%EC%99%80%20%EC%95%99%ED%8E%98%EB%A5%B4%20%EB%B2%95%EC%B9%99.md)

{% raw %}
## 핵심 요약

정자계는 시간에 따라 변하지 않는 전류가 만드는 자기장을 다룬다. 전하가 정지해 있을 때 전기장이 생기듯, 전하가 정상적으로 움직이면 자기장이 생긴다. 핵심 법칙은 로렌츠 힘, 비오-사바르 법칙, 앙페르 회로 법칙, 자기 벡터 포텐셜이다.

## 정자계의 출발점

정전계의 실험적 출발점이 쿨롱 법칙이라면, 정자계의 출발점은 전류가 흐르는 도선 사이에 힘이 작용한다는 실험 사실이다.

- Oersted: 전류가 흐르는 도선이 자기장을 만든다.
- Ampere: 전류가 흐르는 도선 사이에 힘이 작용한다.
- Biot-Savart: 정상 전류가 만드는 자기장을 적분 법칙으로 표현한다.

## 로렌츠 힘

전하 $$q$$ 가 전기장과 자기장 속에서 속도 $$\mathbf{u}$$ 로 움직이면 받는 힘은

$$
\mathbf{F} = q(\mathbf{E}+\mathbf{u}\times\mathbf{B})
$$

이다. 전기력 $$q\mathbf{E}$$ 는 입자의 운동에너지를 바꿀 수 있지만, 자기력 $$q\mathbf{u}\times\mathbf{B}$$ 는 속도에 수직이므로 일을 하지 않는다. 자기력은 입자의 속력보다 방향을 바꾼다.

전류 요소가 받는 힘은

$$
d\mathbf{F} = I\,d\boldsymbol{l}\times\mathbf{B}
$$

로 쓴다.

## 비오-사바르 법칙

전류 요소 $$I\,d\boldsymbol{l}$$ 이 관측점에 만드는 미소 자기장은

$$
d\mathbf{B} = \frac{\mu_0}{4\pi}\frac{I\,d\boldsymbol{l}\times\mathbf{a}_R}{R^2}
$$

이고, 전체 자기장은 전류 경로 전체에 대해 적분한다.

$$
\mathbf{B} = \oint_C \frac{\mu_0}{4\pi}\frac{I\,d\boldsymbol{l}\times\mathbf{a}_R}{R^2}
$$

부피 전류밀도 $$\mathbf{J}$$ 로 표현하면

$$
\mathbf{B} = \int_V \frac{\mu_0}{4\pi}\frac{\mathbf{J}(\mathbf{r}')\times\mathbf{a}_R}{R^2}\,dV'
$$

이는 정전계에서 Coulomb 법칙과 중첩 원리로 전기장을 구하는 방식과 대응된다.

### Worked example — 유한 길이 직선 도선

$$z$$ 축 위, $$z=-a$$ 부터 $$z=+a$$ 까지 전류 $$I$$ 가 흐르는 직선 도선을 생각하자. 수직이등분면 위 거리 $$r$$ 인 관측점(예: $$(r,0,0)$$)에서 비오-사바르를 직접 적분하면($$d\boldsymbol{l}=\mathbf{a}_z\,dz$$, $$\mathbf{R}=(r,0,-z)$$):

$$
\mathbf{B} = \mathbf{a}_\phi\,\frac{\mu_0 I r}{4\pi}\int_{-a}^{a}\frac{dz}{(r^2+z^2)^{3/2}}
= \mathbf{a}_\phi\,\frac{\mu_0 I}{2\pi r}\cdot\frac{a}{\sqrt{r^2+a^2}}
= \mathbf{a}_\phi\,\frac{\mu_0 I}{2\pi r}\sin\theta
$$

여기서 $$\sin\theta = a/\sqrt{r^2+a^2}$$ 는 도선 끝이 관측점에서 보이는 반각이다. $$a\to\infty$$ 이면 $$\sin\theta\to1$$ 이 되어 아래 무한 직선 도선 결과 $$B=\mu_0I/(2\pi r)$$ 로 정확히 수렴한다 — **무한 직선은 유한 직선의 극한**임이 식으로 확인된다.

**숫자 예**: $$I=5\text{A}$$, $$a=0.3\text{m}$$, $$r=0.1\text{m}$$: $$\sin\theta = 0.3/\sqrt{0.01+0.09}=0.3/0.316\approx0.949$$, $$B\approx\dfrac{(4\pi\times10^{-7})(5)}{2\pi(0.1)}(0.949)\approx9.49\ \mu\text{T}$$ — 같은 조건의 무한 도선 값 $$10\ \mu\text{T}$$ 에 이미 $$95\%$$ 근접한다(도선 길이가 거리의 3배만 되어도 무한 근사가 꽤 정확해진다는 실용적 감각).

## 정자계의 기본 방정식

자유공간 정자계에서 중요한 두 식은 다음이다.

$$
\nabla\cdot\mathbf{B}=0,\qquad \nabla\times\mathbf{B}=\mu_0\mathbf{J}
$$

첫 식은 자기 단극자가 없다는 뜻이다. 자기장 선은 시작점과 끝점 없이 닫힌 선을 이룬다. 둘째 식은 전류가 자기장의 회전을 만든다는 뜻이다.

## 앙페르 회로 법칙

비오-사바르 법칙은 항상 쓸 수 있지만 적분이 복잡할 수 있다. 대칭성이 강한 경우에는 앙페르 회로 법칙이 훨씬 빠르다.

$$
\oint_C \mathbf{B}\cdot d\boldsymbol{l} = \mu_0 I_{enc}
$$

또는 자유공간이 아닌 매질을 포함하면 $$\mathbf{H}$$ 를 사용해

$$
\oint_C \mathbf{H}\cdot d\boldsymbol{l} = I_{free,enc}
$$

로 쓴다.

## 대표 대칭 문제

### 무한 직선 도선

반지름 $$r$$ 인 원형 앙페르 경로를 잡으면 자기장은 원주 방향이고 크기는 일정하다.

$$
\mathbf{B} = \frac{\mu_0 I}{2\pi r}\mathbf{a}_\phi
$$

### 균일 전류가 흐르는 원통 도선

도선 내부에서는 포함 전류가 $$r^2$$ 에 비례하므로 $$B$$ 가 $$r$$ 에 비례한다($$B=\mu_0 I r/(2\pi R_0^2)$$, $$R_0$$=도선 반지름). 외부에서는 전체 전류 $$I$$ 를 포함하므로 $$1/r$$ 로 감소한다.

### 긴 솔레노이드

긴 솔레노이드 내부에서는 자기장이 거의 균일하고 외부장은 작다고 근사한다.

$$
\mathbf{B} = \mu_0\frac{N}{l}I\,\mathbf{a}_z
$$

### 무한 전류 시트

표면 전류밀도 $$\mathbf{J}_s$$ 가 흐르는 무한 평면의 양쪽에는 크기가 같은 자기장이 생기며 방향은 서로 반대이다.

$$
B = \mu_0 J_s/2
$$

## 자기 벡터 포텐셜

$$\nabla\cdot\mathbf{B}=0$$ 이므로 자기장은 어떤 벡터 포텐셜의 curl로 쓸 수 있다.

$$
\mathbf{B} = \nabla\times\mathbf{A}
$$

$$\mathbf{A}$$ 는 유일하지 않다. $$\mathbf{A}'=\mathbf{A}+\nabla\psi$$ 도 같은 $$\mathbf{B}$$ 를 만든다(gauge 자유도). 계산을 단순하게 하기 위해 Coulomb gauge

$$
\nabla\cdot\mathbf{A}=0
$$

를 선택할 수 있다. 이때 정자계에서는 벡터 포아송 방정식이 나온다.

$$
\nabla^2\mathbf{A} = -\mu_0\mathbf{J}
$$

이는 정전계의 $$\nabla^2V=-\rho/\varepsilon$$ 과 정확히 대응되는 구조로, $$\mathbf{A}$$ 의 각 직교좌표 성분이 스칼라 포아송 방정식을 만족한다는 뜻이다.

## 자기 쌍극자

작은 전류 루프는 멀리서 자기 쌍극자처럼 보인다. 자기 쌍극자 모멘트는

$$
\mathbf{m} = I S\,\mathbf{a}_n
$$

으로 정의한다. 균일 자기장 속 전류 루프는 순힘은 0일 수 있지만 토크를 받아 회전하려 한다.

$$
\mathbf{T} = \mathbf{m}\times\mathbf{B}
$$

전동기는 이 토크를 이용하되, 계속 같은 방향으로 회전하려면 전류 방향을 적절히 바꾸어야 한다.

## 연결 노트

- [정상 전류와 저항](05-steady-current-and-resistance.md)
- [정자계 II - 자성체와 경계조건](07-magnetostatics-2-materials-and-boundary-conditions.md)
- [시간 변화장과 맥스웰 방정식](08-time-varying-fields-and-maxwell.md)

## 복습 질문

- 로렌츠 힘에서 자기력이 일을 하지 않는 이유를 $$\mathbf{u}\times\mathbf{B}\perp\mathbf{u}$$ 로 설명할 수 있는가?
- 유한 길이 직선 도선의 $$B=\dfrac{\mu_0I}{2\pi r}\sin\theta$$ 를 비오-사바르 적분으로 유도하고, $$a\to\infty$$ 극한이 무한 도선 결과로 수렴함을 보일 수 있는가?
- 앙페르 법칙을 쓰기 좋은 대칭 조건(원통, 무한 평면 등)은 무엇인가?
- $$\nabla^2\mathbf{A}=-\mu_0\mathbf{J}$$ 가 정전계의 포아송 방정식과 어떻게 대응되는가?
- 자기 쌍극자가 균일 자기장에서 순힘 없이 토크만 받는 이유는?

{% endraw %}

---

이전: [05. 정상 전류와 저항](05-steady-current-and-resistance.md) · 다음: [07. 정자계 II - 자성체와 경계조건](07-magnetostatics-2-materials-and-boundary-conditions.md)
