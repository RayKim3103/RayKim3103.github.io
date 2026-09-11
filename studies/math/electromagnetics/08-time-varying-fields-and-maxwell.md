---
layout: page
title: "08. 시간 변화장과 맥스웰 방정식"
permalink: /studies/math/electromagnetics/08-time-varying-fields-and-maxwell/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electromagnetics/lecture_notes/09%20%EC%8B%9C%EA%B0%84%20%EB%B3%80%ED%99%94%EC%9E%A5%EA%B3%BC%20%EB%A7%A5%EC%8A%A4%EC%9B%B0%20%EB%B0%A9%EC%A0%95%EC%8B%9D.md)

{% raw %}
## 핵심 요약

시간 변화 전자기장은 정전계와 정자계를 하나의 동역학으로 연결한다. 시간에 따라 변하는 자기장은 비보존 전기장을 만들고, 시간에 따라 변하는 전기 플럭스는 변위 전류로 작용해 자기장을 만든다. 이 두 항을 포함하면 맥스웰 방정식이 완성되고, 전자기파와 빛의 전자기적 성질이 자연스럽게 나온다.

## 정적 장의 복습

정전계와 정자계의 핵심 식은 다음이었다.

$$
\nabla\cdot\mathbf{D}=\rho,\qquad \nabla\times\mathbf{E}=0,\qquad \nabla\cdot\mathbf{B}=0,\qquad \nabla\times\mathbf{H}=\mathbf{J}
$$

하지만 시간 변화가 생기면 $$\nabla\times\mathbf{E}=0$$ 과 $$\nabla\times\mathbf{H}=\mathbf{J}$$ 만으로는 실험과 전하 보존을 동시에 설명할 수 없다.

## 패러데이 법칙이 필요한 이유

자기장 속 코일을 움직이면 자기력 $$q(\mathbf{v}\times\mathbf{B})$$ 때문에 기전력이 생긴다. 반대로 자석을 움직이고 회로가 정지해 있어도 같은 유도 전류가 생긴다. 관성계에 따라 설명이 달라지지만 관측되는 현상은 같으므로, 더 일반적인 법칙이 필요하다.

패러데이 법칙은 자기 플럭스 변화가 유도기전력을 만든다고 말한다.

$$
V_{ind} = -\frac{d\Phi_B}{dt},\qquad \Phi_B = \int_S \mathbf{B}\cdot d\mathbf{S}
$$

정지한 회로에서는

$$
\oint_C \mathbf{E}\cdot d\boldsymbol{l} = -\int_S \frac{\partial\mathbf{B}}{\partial t}\cdot d\mathbf{S},\qquad \nabla\times\mathbf{E}=-\frac{\partial\mathbf{B}}{\partial t}
$$

가 된다.

### Worked example — 원형 루프에 유도되는 기전력

반지름 $$5\text{cm}$$ 원형 루프($$N=1$$)를 관통하는 균일 자기장이 $$B(t) = 0.2\sin(120\pi t)\text{ T}$$ 로 변한다고 하자($$60\text{Hz}$$ 교류 자기장). $$\Phi_B(t) = B(t)\cdot\pi r^2 = 0.2\sin(120\pi t)\cdot\pi(0.05)^2$$:

$$
V_{ind} = -\frac{d\Phi_B}{dt} = -\pi(0.05)^2 \times 0.2\times120\pi\cos(120\pi t) \approx -0.0711\cos(120\pi t)\ \text{V}
$$

진폭 약 $$71\text{ mV}$$. 같은 루프를 $$N=100$$ 번 감으면($$\lambda=N\Phi_B$$) 유도 전압 진폭은 그대로 $$100$$배인 약 $$7.1\text{ V}$$ — 변압기·발전기에서 권선수를 늘리는 이유가 바로 이 선형 관계다.

## 유도 전기장

정전기장은 보존장이므로 닫힌 경로 적분이 0이다. 그러나 시간에 따라 변하는 자기장이 만드는 유도 전기장은 비보존장이다.

$$
\oint \mathbf{E}\cdot d\boldsymbol{l} \ne 0
$$

도선이 없어도 공간 자체에 유도 전기장이 생기며, 도선이 있으면 그 유도 전기장이 전류를 흐르게 한다.

## 렌츠 법칙

음의 부호는 유도 전류가 원래 자기 플럭스 변화를 방해하는 방향으로 흐른다는 뜻이다. 이는 에너지 보존과 연결된다. 만약 유도 전류가 플럭스 변화를 돕는 방향으로 생기면 자기 플럭스와 전류가 무한히 증가할 수 있어 물리적으로 불가능하다.

## 운동 기전력과 발전기

자기장 속에서 길이 $$l$$ 인 도체 막대가 속도 $$v$$ 로 움직이면

$$
V_{ind} = Blv
$$

형태의 운동 기전력이 생긴다. 유도 전류가 흐르면 로렌츠 힘이 운동을 방해하고, 외부에서 한 일이 저항 열로 소모된다. 발전기는 회전하는 코일의 자기 플럭스가 시간에 따라 바뀌는 원리를 이용한다 — 넓이 $$S$$, 권선수 $$N$$ 인 코일이 균일자기장 $$B$$ 안에서 각속도 $$\omega$$ 로 회전하면 $$\Phi_B(t)=BS\cos\omega t$$, $$V_{ind}(t) = N\omega BS\sin\omega t$$ 로 정현파 기전력이 그대로 만들어진다(교류 발전기의 원리).

## 인덕턴스

인덕터는 자기장에 에너지를 저장한다. 플럭스 linkage와 전류의 비가 인덕턴스이다.

$$
L = \lambda/I,\qquad \lambda=N\Phi
$$

긴 솔레노이드에서는

$$
B = \mu\frac{N}{l}I,\qquad L = \mu\frac{N^2A}{l}
$$

**숫자 예**: 공심($$\mu=\mu_0$$) 솔레노이드, $$N=500$$ 회, 길이 $$l=20\text{cm}$$, 단면적 $$A=1\text{cm}^2=10^{-4}\text{m}^2$$:
$$
L = (4\pi\times10^{-7})\frac{500^2\times10^{-4}}{0.2} \approx 1.57\times10^{-4}\text{ H} = 0.157\text{ mH}
$$
철심(예: $$\mu_r=1000$$)을 넣으면 $$L$$ 은 그대로 $$1000$$배가 되어 약 $$157\text{ mH}$$ — 인덕터 설계에서 코어 재질이 왜 중요한지 숫자로 드러난다.

자기 에너지는

$$
W_m = \frac12 LI^2,\qquad w_m = \frac12 \mathbf{B}\cdot\mathbf{H}
$$

로 쓴다. 인덕터 전류를 갑자기 바꾸기 어려운 이유는 전류 변화가 자기 플럭스 변화를 만들고, 패러데이 법칙에 의해 이를 방해하는 유도 전압이 생기기 때문이다.

## 상호 인덕턴스와 변압기

한 회로의 전류가 만든 자기 플럭스가 다른 회로를 통과하면 상호 인덕턴스가 생긴다.

$$
L_{12} = N_2\Phi_{12}/I_1,\qquad L_{21}=L_{12}
$$

변압기는 시간 변화 자기 플럭스가 다른 권선에 전압을 유도하는 구조이다. 결합 정도, 권선수, 자성체 코어가 유도 전압과 에너지 전달을 결정한다.

## 와전류와 자기부상

시간 변화 자기장이나 움직이는 자석은 도체 내부에 닫힌 전류 루프를 유도한다. 이것이 와전류이다. 와전류는 열 손실을 만들 수 있지만, 전자기 브레이크, 금속 탐지기, 자기부상 같은 응용에도 쓰인다.

## 변위 전류

기존 앙페르 법칙

$$
\nabla\times\mathbf{H} = \mathbf{J}
$$

은 시간 변화 상황에서 전하 보존과 충돌한다. 양변에 divergence를 취하면 좌변은 항상 0인데($$\nabla\cdot(\nabla\times\mathbf{H})\equiv0$$, 벡터 항등식), 연속 방정식은 일반적으로 $$\nabla\cdot\mathbf{J}=-\partial\rho/\partial t$$ 이기 때문이다.

맥스웰은 변위 전류 항을 추가했다.

$$
\nabla\times\mathbf{H} = \mathbf{J}+\frac{\partial\mathbf{D}}{\partial t}
$$

적분형은

$$
\oint_C \mathbf{H}\cdot d\boldsymbol{l} = \int_S \Big(\mathbf{J}+\frac{\partial\mathbf{D}}{\partial t}\Big)\cdot d\mathbf{S}
$$

이다. 충전 중인 커패시터 판 사이에는 도전 전류가 직접 흐르지 않지만, 시간 변화 전기 플럭스가 자기장을 만든다. 이것이 회로 바깥 공간까지 전자기장을 일관되게 설명한다. 이제 $$\nabla\cdot(\nabla\times\mathbf{H})=\nabla\cdot\mathbf{J}+\partial(\nabla\cdot\mathbf{D})/\partial t = \nabla\cdot\mathbf{J}+\partial\rho/\partial t=0$$ 이 연속 방정식과 정확히 일치해, 앞서 지적한 모순이 해소된다.

## 완성된 맥스웰 방정식

$$
\nabla\cdot\mathbf{D}=\rho,\qquad \nabla\times\mathbf{E}=-\frac{\partial\mathbf{B}}{\partial t},\qquad \nabla\cdot\mathbf{B}=0,\qquad \nabla\times\mathbf{H}=\mathbf{J}+\frac{\partial\mathbf{D}}{\partial t}
$$

적분형은 다음과 같다.

$$
\oint_S \mathbf{D}\cdot d\mathbf{S}=Q_{enc},\quad
\oint_C \mathbf{E}\cdot d\boldsymbol{l}=-\frac{d}{dt}\int_S\mathbf{B}\cdot d\mathbf{S},\quad
\oint_S \mathbf{B}\cdot d\mathbf{S}=0,\quad
\oint_C \mathbf{H}\cdot d\boldsymbol{l}=\int_S\mathbf{J}\cdot d\mathbf{S}+\frac{d}{dt}\int_S\mathbf{D}\cdot d\mathbf{S}
$$

이 네 식에 물질 관계식 $$\mathbf{D}=\varepsilon\mathbf{E}$$, $$\mathbf{B}=\mu\mathbf{H}$$, $$\mathbf{J}=\sigma\mathbf{E}$$ 와 로렌츠 힘 법칙을 더하면 거시적 전자기 현상의 기본 틀이 완성된다.

## 포텐셜 함수

항상 $$\nabla\cdot\mathbf{B}=0$$ 이므로

$$
\mathbf{B} = \nabla\times\mathbf{A}
$$

라고 둘 수 있다. 패러데이 법칙을 함께 쓰면 시간 변화장에서 전기장은

$$
\mathbf{E} = -\nabla V - \frac{\partial\mathbf{A}}{\partial t}
$$

가 된다. 첫 항은 전하 축적이 만든 전기장이고, 둘째 항은 시간 변화 자기 포텐셜이 만든 유도 전기장이다.

## 전자기파

source-free 자유공간에서

$$
\rho=0,\quad \mathbf{J}=0,\quad \mathbf{D}=\varepsilon_0\mathbf{E},\quad \mathbf{B}=\mu_0\mathbf{H}
$$

라 두면 맥스웰 방정식에서 파동방정식이 나온다.

$$
\nabla^2\mathbf{E} - \mu_0\varepsilon_0\frac{\partial^2\mathbf{E}}{\partial t^2} = 0,\qquad \nabla^2\mathbf{H} - \mu_0\varepsilon_0\frac{\partial^2\mathbf{H}}{\partial t^2} = 0
$$

전파 속도는

$$
c = \frac{1}{\sqrt{\mu_0\varepsilon_0}}
$$

이고 이는 빛의 속도와 같다. 따라서 빛은 전자기파로 해석된다.

평면파의 한 예는

$$
\mathbf{E} = \mathbf{a}_x E_0\cos(kz-\omega t),\qquad \mathbf{B} = \mathbf{a}_y B_0\cos(kz-\omega t)
$$

이며, $$\mathbf{E}$$, $$\mathbf{B}$$, 진행 방향은 서로 수직이다.

## 페이저와 주파수 영역

선형 매질에서 정현파 source는 같은 주파수의 정현파 장을 만든다. 따라서 시간 미분은 페이저 영역에서 $$j\omega$$ 곱으로 바뀐다.

$$
\partial/\partial t \;\longrightarrow\; j\omega
$$

맥스웰 방정식도 페이저 형태로 쓸 수 있고, 균질 매질에서는 Helmholtz 방정식으로 이어진다.

$$
\nabla^2\mathbf{E}+k^2\mathbf{E}=0,\qquad \nabla^2\mathbf{H}+k^2\mathbf{H}=0
$$

## 이후 과목으로의 연결

시간 변화장 이후에는 평면 전자기파, 전송선, 도파관, 공진기, 안테나가 자연스럽게 이어진다. 회로 이론은 quasi-static 근사에서 나온 저주파 모델이고, 고주파에서는 전파 지연과 파동성을 직접 고려해야 한다.

## 연결 노트

- [정전계 I - 쿨롱 법칙과 가우스 법칙](03-electrostatics-1-coulomb-and-gauss.md)
- [정상 전류와 저항](05-steady-current-and-resistance.md)
- [정자계 I - 비오-사바르와 앙페르 법칙](06-magnetostatics-1-biot-savart-and-ampere.md)
- [정자계 II - 자성체와 경계조건](07-magnetostatics-2-materials-and-boundary-conditions.md)

## 복습 질문

- $$60\text{Hz}$$, $$0.2\text{T}$$ 진폭 자기장이 반지름 $$5\text{cm}$$ 루프를 관통할 때 유도 기전력 진폭을 계산할 수 있는가? 권선수를 늘리면 왜 비례해서 커지는가?
- 회전 코일 발전기에서 $$V_{ind}(t)=N\omega BS\sin\omega t$$ 가 나오는 과정을 설명할 수 있는가?
- 공심 솔레노이드의 인덕턴스를 계산하고, 철심을 넣으면 왜 $$\mu_r$$ 배가 되는지 설명할 수 있는가?
- 변위 전류 항이 없으면 왜 연속 방정식과 모순되는지, $$\nabla\cdot(\nabla\times\mathbf{H})=0$$ 논증으로 설명할 수 있는가?
- 완성된 맥스웰 방정식 네 개를 쓰고, source-free 조건에서 파동방정식이 나오는 과정을 설명할 수 있는가?

{% endraw %}

---

이전: [07. 정자계 II - 자성체와 경계조건](07-magnetostatics-2-materials-and-boundary-conditions.md)
