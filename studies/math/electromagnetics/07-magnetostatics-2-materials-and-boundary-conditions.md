---
layout: page
title: "07. 정자계 II - 자성체와 경계조건"
permalink: /studies/math/electromagnetics/07-magnetostatics-2-materials-and-boundary-conditions/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electromagnetics/lecture_notes/08%20%EC%A0%95%EC%9E%90%EA%B3%84%20II%20-%20%EC%9E%90%EC%84%B1%EC%B2%B4%EC%99%80%20%EA%B2%BD%EA%B3%84%EC%A1%B0%EA%B1%B4.md)

{% raw %}
## 핵심 요약

자성체가 있는 정자계에서는 자유공간의 $$\mathbf{B}$$ 만으로는 물질 반응을 분리해 보기 어렵다. 물질 내부의 자기 쌍극자 정렬을 자화 $$\mathbf{M}$$ 으로 표현하고, 자유 전류만 다루기 위해 보조장 $$\mathbf{H}$$ 를 도입한다. 이 장은 반자성·상자성·강자성, 자화 전류, 투자율, 자기 경계조건, 홀 효과, 자기공명 응용까지 연결한다.

## 물질의 자기적 분류

강의는 물질을 전기적·자기적 성질에 따라 비교한다.

- 유전체: 전도도는 매우 작고 유전율 효과가 중요하다.
- 도체: 전도도가 크며 전류가 잘 흐른다.
- 강자성체: 철, 니켈, 코발트처럼 자기적 반응이 매우 크다.

자기적 반응은 크게 세 가지로 나뉜다.

| 분류 | 특징 | $$\chi_m$$ | $$\mu_r$$ |
|---|---|---|---|
| 반자성 | 외부 자기장을 약하게 반대하는 방향으로 자화 | $$<0$$ (매우 작음) | $$\lesssim1$$ |
| 상자성 | 외부 자기장을 약하게 돕는 방향으로 자화 | $$>0$$ (작음) | $$\gtrsim1$$ |
| 강자성 | 도메인이 정렬되며 매우 큰 자화와 히스테리시스 | $$\gg0$$ | $$\sim10^2$$–$$10^5$$ |

반자성과 상자성은 엄밀히 양자역학적 현상이다. 강자성체에서는 외부 자기장을 걸면 magnetic domain이 정렬되고, 열이나 충격으로 정렬을 흐트러뜨릴 수 있다.

## 자기 쌍극자와 자화

작은 전류 루프는 자기 쌍극자 모멘트 $$\mathbf{m}$$ 을 가진다.

$$
\mathbf{m} = IS\,\mathbf{a}_n
$$

자기장 속 자기 쌍극자는 토크를 받는다.

$$
\mathbf{T} = \mathbf{m}\times\mathbf{B}
$$

자화는 단위 부피당 자기 쌍극자 모멘트이다.

$$
\mathbf{M} = \lim_{\Delta v\to0}\frac{\sum\mathbf{m}}{\Delta v}
$$

자화는 물질 내부의 미시적인 순환 전류를 거시적으로 나타낸 것이다.

## 자화 전류

자화 $$\mathbf{M}$$ 은 등가 전류로 표현할 수 있다.

$$
\mathbf{J}_m = \nabla\times\mathbf{M},\qquad \mathbf{J}_{ms} = \mathbf{M}\times\mathbf{a}_n
$$

균일한 자화에서는 부피 자화 전류 $$\mathbf{J}_m$$ 은 0일 수 있지만, 표면 자화 전류 $$\mathbf{J}_{ms}$$ 는 남을 수 있다. 균일 자화된 원통은 표면 전류가 흐르는 솔레노이드처럼 해석할 수 있다.

## H장의 도입

자화 전류까지 포함하면

$$
\nabla\times\mathbf{B} = \mu_0(\mathbf{J}_{free}+\mathbf{J}_m)
$$

이다. 자유 전류만 source로 남기기 위해 $$\mathbf{H}$$ 를 정의한다.

$$
\mathbf{H} = \mathbf{B}/\mu_0-\mathbf{M},\qquad \nabla\times\mathbf{H}=\mathbf{J}_{free},\qquad \oint\mathbf{H}\cdot d\boldsymbol{l}=I_{free,enc}
$$

즉 $$\mathbf{H}$$ 는 물질 내부의 자화 효과를 분리하고 자유 전류가 만드는 순환을 다루기 위한 장이다.

## 선형 등방 매질

선형 등방 매질에서는

$$
\mathbf{M}=\chi_m\mathbf{H},\qquad \mathbf{B}=\mu\mathbf{H},\qquad \mu=\mu_0\mu_r,\qquad \mu_r=1+\chi_m
$$

로 쓴다.

- 반자성체: $$\chi_m<0$$, $$\mu_r<1$$
- 상자성체: $$\chi_m>0$$, $$\mu_r$$ 이 1보다 약간 큼
- 강자성체: $$\mu_r$$ 이 매우 크고 비선형·히스테리시스 가능

## 자기 경계조건

맥스웰 방정식의 적분형에서 경계조건이 나온다.

**법선 B**: 자기 단극자가 없으므로 $$B_{1n}=B_{2n}$$ — $$\mathbf{B}$$ 의 법선 성분은 경계에서 연속이다.

**접선 H**: 경계에 자유 표면 전류밀도 $$\mathbf{J}_s$$ 가 있으면 $$\mathbf{a}_n\times(\mathbf{H}_2-\mathbf{H}_1)=\mathbf{J}_s$$. 자유 표면 전류가 없으면 $$\mathbf{H}$$ 의 접선 성분은 연속이다.

### Worked example — 자기장선의 굴절

$$\mathbf{J}_s=0$$ 인 두 매질($$\mu_1,\mu_2$$) 경계에서 법선과 이루는 각을 $$\theta_1,\theta_2$$ 라 하자. $$B_{1n}=B_{2n}$$ 은 $$B_1\cos\theta_1=B_2\cos\theta_2$$, $$H_{1t}=H_{2t}$$ 는 $$\dfrac{B_1\sin\theta_1}{\mu_1}=\dfrac{B_2\sin\theta_2}{\mu_2}$$ 로 쓸 수 있다. 두 식을 나누면

$$
\frac{\tan\theta_1}{\mu_1} = \frac{\tan\theta_2}{\mu_2}
\quad\Longrightarrow\quad
\frac{\tan\theta_1}{\tan\theta_2} = \frac{\mu_1}{\mu_2}
$$

**숫자 예**: 공기($$\mu_{r1}=1$$)에서 철($$\mu_{r2}=1000$$)로 $$\theta_1=10°$$ 로 입사하면 $$\tan\theta_2 = \tan(10°)\times1000 \approx 176.3 \Rightarrow \theta_2\approx89.7°$$ — 자기장선이 철 내부에서는 경계면에 거의 나란해진다. 이것이 **자기 차폐(magnetic shielding)**의 원리: 고투자율 물질에 들어간 field line은 거의 그 물질을 따라 흐르다 빠져나가므로, 물질로 둘러싸인 내부 영역은 외부 자기장으로부터 보호된다.

## 자기력과 홀 효과

전류가 흐르는 도체는 자기장 속에서 힘을 받는다.

$$
d\mathbf{F} = I\,d\boldsymbol{l}\times\mathbf{B}
$$

평행한 두 도선에 같은 방향 전류가 흐르면 서로 끌어당기고, 반대 방향이면 밀어낸다.

홀 효과는 움직이는 carrier가 자기력을 받아 한쪽으로 몰리면서 횡방향 전기장이 생기는 현상이다.

$$
\mathbf{F}=q(\mathbf{E}+\mathbf{v}\times\mathbf{B}),\qquad V_H = E_y d
$$

이를 이용하면 반도체의 carrier 부호, carrier 농도, 전도도를 추정할 수 있다.

## MRI와 자화 응용

강의는 자기 감수율과 MRI 응용을 소개한다.

- fMRI는 혈류 변화와 산소화 상태에 따른 자기적 차이를 이용한다.
- QSM은 조직의 자기 감수율 분포를 영상화한다.
- 철 침착, 석회화, 미세출혈 구분 같은 응용이 가능하다.
- 수소 원자핵은 외부 자기장 안에서 순자화와 precession을 보이며, gradient를 사용하면 공간 위치를 주파수 성분으로 부호화할 수 있다.

## 연결 노트

- [정자계 I - 비오-사바르와 앙페르 법칙](06-magnetostatics-1-biot-savart-and-ampere.md)
- [시간 변화장과 맥스웰 방정식](08-time-varying-fields-and-maxwell.md)

## 복습 질문

- $$\mathbf{H}=\mathbf{B}/\mu_0-\mathbf{M}$$ 을 도입하는 이유(자유 전류만 source로 남기기 위해)를 설명할 수 있는가?
- 반자성·상자성·강자성을 $$\chi_m$$, $$\mu_r$$ 부호와 크기로 구분할 수 있는가?
- $$\tan\theta_1/\tan\theta_2=\mu_1/\mu_2$$ 를 $$B_n$$ 연속·$$H_t$$ 연속으로부터 유도할 수 있는가?
- 왜 고투자율 물질 내부에서 field line이 경계면에 거의 나란해지는지, 그리고 이것이 자기 차폐와 어떻게 연결되는지 설명할 수 있는가?
- 홀 효과에서 $$V_H$$ 로부터 carrier 부호와 농도를 어떻게 추정하는가?

{% endraw %}

---

이전: [06. 정자계 I - 비오-사바르와 앙페르 법칙](06-magnetostatics-1-biot-savart-and-ampere.md) · 다음: [08. 시간 변화장과 맥스웰 방정식](08-time-varying-fields-and-maxwell.md)
