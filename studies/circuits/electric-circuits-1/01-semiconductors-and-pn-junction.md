---
layout: page
title: "01. 반도체와 PN 접합"
permalink: /studies/circuits/electric-circuits-1/01-semiconductors-and-pn-junction/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/01%20%EB%B0%98%EB%8F%84%EC%B2%B4%EC%99%80%20PN%20%EC%A0%91%ED%95%A9.md)

{% raw %}
## 한눈에 보기

이 장은 전자회로 1의 출발점인 반도체 물성을 정리한다. 원자 결합, 도핑, 캐리어 농도, drift 전류, diffusion 전류, PN 접합의 built-in potential, 순방향/역방향 바이어스, 다이오드 전류식까지 이어진다.

```text
원자와 공유결합 -> intrinsic/extrinsic semiconductor
-> n형/p형 도핑 -> drift/diffusion current
-> PN junction -> built-in potential -> diode equation
```

## 강의 전체 흐름

전자회로 1에서는 기초회로이론에서 다룬 선형 수동소자만으로는 설명하기 어려운 능동소자를 다룬다.

- 반도체 기초
- 다이오드
- 트랜지스터
- BJT
- MOSFET
- 증폭기
- 전류원과 전류 미러
- CMOS 인버터

회로 문제는 그림을 직접 그리며 푸는 훈련이 중요하다. 특히 동작 영역을 먼저 판단하고, 그 영역에 맞는 등가모델을 적용해야 한다.

## 원자와 최외각 전자

원자의 화학적 성질은 최외각 껍질의 전자, 즉 valence electron에 의해 결정된다. 원자는 안정한 전자 배치를 만들기 위해 전자를 잃거나 얻거나 공유한다.

예시는 다음과 같다.

- Na는 전자를 하나 잃어 $Na^+$가 되기 쉽다.
- Cl은 전자를 하나 얻어 $Cl^-$가 되기 쉽다.
- Ne, He, Ar 같은 비활성 기체는 이미 안정한 최외각 전자 구조를 갖는다.

Si는 4족 원소로 최외각 전자가 4개이다. Si 원자들은 서로 공유결합을 형성하여 결정 구조를 만든다.

## 공유결합과 자유전자

Si 결정에서 전자가 공유결합에 묶여 있으면 자유롭게 이동하지 못한다. 하지만 에너지를 받으면 공유결합이 끊어져 자유전자가 생기고, 전자가 빠져나간 자리에 정공이 생긴다.

- 자유전자: 음전하 캐리어
- 정공: 양전하처럼 움직이는 캐리어

공유결합이 끊어져 자유전자와 정공이 생기면 전류가 흐를 수 있다.

## 캐리어 농도 표기

앞으로 자주 쓰는 농도 표기는 다음과 같다.

| 기호 | 의미 |
|---|---|
| $n$ | 전자 농도 |
| $p$ | 정공 농도 |
| $n_i$ | intrinsic semiconductor의 전자 농도 |
| $p_i$ | intrinsic semiconductor의 정공 농도 |
| $N_D$ | donor 도핑 농도 |
| $N_A$ | acceptor 도핑 농도 |

순수 반도체에서는 전자와 정공이 쌍으로 생성되므로

$$
n_i=p_i
$$

이다.

질량작용 법칙은 다음과 같다.

$$
np=n_i^2
$$

상온의 Si는 $n_i \approx 1.08\times 10^{10}/cm^3$ 정도로 매우 작다. Si 원자 수에 비해 자유 캐리어가 매우 적으므로 도핑이 필요하다.

## 도핑

도핑은 Si에 불순물을 넣어 캐리어 농도를 의도적으로 조절하는 과정이다.

### n형 반도체

n형 반도체는 donor를 넣어 전자를 다수 캐리어로 만든다. 대표적으로 P 같은 5족 원소를 넣으면 공유결합 후 남는 전자가 생긴다.

- majority carrier: electron
- minority carrier: hole

$N_D \gg n_i$이면

$$
n \approx N_D
$$

$$
p \approx \frac{n_i^2}{N_D}
$$

### p형 반도체

p형 반도체는 acceptor를 넣어 정공을 다수 캐리어로 만든다. 대표적으로 B 같은 3족 원소를 넣으면 전자가 부족한 자리가 생긴다.

- majority carrier: hole
- minority carrier: electron

$N_A \gg p_i$이면

$$
p \approx N_A
$$

$$
n \approx \frac{n_i^2}{N_A}
$$

## Drift current

Drift 전류는 전기장에 의해 캐리어가 이동하면서 생기는 전류이다. 전압 차이가 있으면 전기장이 생기고, 전자와 정공이 이동한다.

캐리어 속도는 전기장에 비례한다.

$$
v_e=-\mu_n E
$$

$$
v_h=\mu_p E
$$

여기서 $\mu_n$, $\mu_p$는 각각 전자와 정공의 mobility이다.

전류밀도는 다음처럼 정리된다.

$$
J_e=q n \mu_n E
$$

$$
J_h=q p \mu_p E
$$

따라서 총 drift 전류밀도는

$$
J_{\text{drift}}=q(n\mu_n+p\mu_p)E
$$

이다.

## Diffusion current

Diffusion 전류는 캐리어 농도 차이에 의해 생기는 전류이다. 캐리어는 농도가 높은 곳에서 낮은 곳으로 확산된다.

전자 diffusion 전류밀도:

$$
J_{e,\text{diff}}=qD_n\frac{dn}{dx}
$$

정공 diffusion 전류밀도:

$$
J_{h,\text{diff}}=-qD_p\frac{dp}{dx}
$$

총 diffusion 전류밀도:

$$
J_{\text{diff}}=qD_n\frac{dn}{dx}-qD_p\frac{dp}{dx}
$$

부호는 캐리어의 전하와 conventional current 방향 때문에 달라진다.

## Einstein relation

Drift와 diffusion은 서로 연결되어 있다.

$$
\frac{D_n}{\mu_n}=\frac{kT}{q}
$$

$$
\frac{D_p}{\mu_p}=\frac{kT}{q}
$$

여기서

$$
V_T=\frac{kT}{q}
$$

를 thermal voltage라고 하며, 상온에서 약 $26mV$이다. BJT와 다이오드 식에서 매우 자주 등장한다.

## PN 접합

PN 접합은 p형 반도체와 n형 반도체를 붙인 구조이다. 이 장에서는 세 상태를 구분한다.

- 평형 상태: built-in potential 형성
- 순방향 바이어스: 다이오드 전류 발생
- 역방향 바이어스: depletion region 확장과 가변 커패시터 동작

## 평형 상태와 depletion region

p형과 n형을 접합하면 농도 차이 때문에 전자와 정공이 서로 반대쪽으로 확산된다. 접합 근처에서 전자와 정공이 재결합하면 움직일 수 없는 이온만 남는다.

이 영역을 depletion region이라고 한다.

- n쪽에는 양전하 donor ion이 남는다.
- p쪽에는 음전하 acceptor ion이 남는다.
- 고정 이온들이 전기장을 만든다.
- 전기장은 drift 전류를 만든다.

평형 상태에서는 diffusion 전류와 drift 전류가 같아져 전체 전류가 0이 된다.

$$
I_{\text{diff}}=I_{\text{drift}}
$$

## Built-in potential

PN 접합의 내부 전위 장벽은 다음과 같다.

$$
V_0=V_T\ln\left(\frac{N_A N_D}{n_i^2}\right)
$$

예를 들어

$$
N_A=2\times 10^{16}/cm^3
$$

$$
N_D=4\times 10^{16}/cm^3
$$

$$
n_i=1.08\times 10^{10}/cm^3
$$

상온에서 $V_T\approx 26mV$이면 built-in potential은 약 $768mV$가 된다.

## 역방향 바이어스

역방향 바이어스를 걸면 depletion region이 넓어진다. 캐리어가 접합을 통과하기 어려워져 전류가 거의 흐르지 않는다.

이때 depletion region은 절연체처럼 동작하고, p/n 양쪽의 도전 영역은 도체처럼 동작한다. 따라서 PN 접합은 커패시터처럼 볼 수 있다.

$$
C=\epsilon\frac{S}{d}
$$

역방향 전압 $V_R$이 커지면 depletion width $d$가 커지고, 커패시턴스는 작아진다. 이 성질은 varactor diode의 원리이다.

## 순방향 바이어스

순방향 바이어스를 걸면 내부 장벽이 낮아지고 depletion region이 좁아진다. 이때 다수 캐리어가 접합을 넘어가면서 diffusion current가 크게 증가한다.

다이오드 전류는 Shockley 식으로 모델링된다.

$$
I=I_S\left(e^{V_F/V_T}-1\right)
$$

순방향 전압이 충분히 크면 $-1$은 무시할 수 있어

$$
I\approx I_S e^{V_F/V_T}
$$

로 근사한다.

## 실용적 다이오드 모델

실제 회로 해석에서는 이상적인 지수식을 그대로 쓰기보다 근사 모델을 사용한다.

- ideal diode model: 켜지면 전압강하 0V, 꺼지면 전류 0
- constant voltage model: 켜지면 약 $0.7V$ 또는 $0.8V$ 전압강하
- exponential model: $I=I_S(e^{V/V_T}-1)$ 사용

## 핵심 정리

- 반도체 전류는 drift와 diffusion으로 나뉜다.
- 도핑은 캐리어 농도를 제어한다.
- n형은 전자가 다수 캐리어이고 p형은 정공이 다수 캐리어이다.
- PN 접합의 평형 상태에서는 diffusion 전류와 drift 전류가 균형을 이룬다.
- built-in potential은 $V_0=V_T\ln(N_A N_D/n_i^2)$로 주어진다.
- 순방향 바이어스에서는 지수적으로 전류가 증가한다.
- 역방향 바이어스에서는 depletion region이 넓어지고 커패시터처럼 동작한다.

## 연결되는 노트

- [다이오드 응용](02-diode-applications.md)
- [BJT 기본 동작](03-bjt-operation.md)
- [MOSFET 기본 동작](05-mosfet-operation.md)

## 복습 체크리스트

- [ ] $np=n_i^2$의 의미를 설명할 수 있다.
- [ ] n형과 p형 반도체의 majority carrier를 구분할 수 있다.
- [ ] drift 전류와 diffusion 전류의 발생 원인을 비교할 수 있다.
- [ ] Einstein relation과 thermal voltage를 쓸 수 있다.
- [ ] PN 접합의 depletion region 형성 과정을 설명할 수 있다.
- [ ] built-in potential 식을 적용할 수 있다.
- [ ] Shockley diode equation을 회로 해석에 사용할 수 있다.

{% endraw %}

---

다음: [02. 다이오드 응용](02-diode-applications.md)
