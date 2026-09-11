---
layout: page
title: "04. 정전계 II - 유전체와 커패시턴스"
permalink: /studies/math/electromagnetics/04-electrostatics-2-dielectrics-and-capacitance/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electromagnetics/lecture_notes/05%20%EC%A0%95%EC%A0%84%EA%B3%84%20II%20-%20%EC%9C%A0%EC%A0%84%EC%B2%B4%EC%99%80%20%EC%BB%A4%ED%8C%A8%EC%8B%9C%ED%84%B4%EC%8A%A4.md)

{% raw %}
## 핵심 요약

유전체는 자유전하가 거의 움직이지 않지만, 외부 전기장에 의해 내부의 묶인 전하가 미세하게 변위되어 분극을 만든다. 분극은 유전체 내부 전기장을 바꾸므로, 전기장을 직접 $$\mathbf{E}$$ 만으로 다루기보다 전기 플럭스 밀도 $$\mathbf{D}$$ 를 도입한다. 이후 경계조건, 커패시턴스, 정전 에너지, 전기력까지 연결된다.

## 유전체와 분극

비극성 물질은 외부 전기장이 없을 때 평균 쌍극자 모멘트가 0이다. 외부 전기장이 걸리면 양전하와 음전하의 중심이 미세하게 분리되어 유도 쌍극자가 생긴다. 극성 물질은 물 분자처럼 영구 쌍극자 모멘트를 가지며, 외부 전기장이 걸리면 쌍극자들이 정렬하려는 경향을 보인다.

분극 벡터는 단위 부피당 전기 쌍극자 모멘트이다.

$$
\mathbf{P} = \lim_{\Delta v\to0}\frac{\sum \mathbf{p}}{\Delta v}
$$

분극은 묶인 전하를 만든다.

$$
\rho_p = -\nabla\cdot\mathbf{P},\qquad \rho_{ps} = \mathbf{P}\cdot\mathbf{a}_n
$$

균일한 분극에서는 부피 분극 전하가 사라질 수 있지만, 표면 분극 전하는 남을 수 있다.

## 전기 플럭스 밀도 D

분극을 가우스 법칙에 직접 넣으면 매번 묶인 전하를 계산해야 한다. 이를 피하기 위해 $$\mathbf{D}$$ 를 정의한다.

$$
\mathbf{D} = \varepsilon_0\mathbf{E}+\mathbf{P},\qquad \nabla\cdot\mathbf{D}=\rho_{free},\qquad \oint\mathbf{D}\cdot d\mathbf{S}=Q_{free}
$$

선형, 등방, 균질 유전체에서는

$$
\mathbf{P}=\varepsilon_0\chi_e\mathbf{E},\qquad \mathbf{D}=\varepsilon\mathbf{E},\qquad \varepsilon=\varepsilon_0\varepsilon_r
$$

상대 유전율 $$\varepsilon_r$$ 은 물질이 전기장에 얼마나 잘 분극되는지 나타낸다. 강의에서는 공기 약 1, 유리 4-10, 물 약 80, 기름 2-3 정도의 값을 예로 든다.

## 유전체 경계조건

두 매질의 경계에서는 장을 법선 성분과 접선 성분으로 나누어 생각한다.

**법선 성분**: $$D_{1n}-D_{2n} = \rho_{s,free}$$. 자유 표면전하가 없으면 $$\mathbf{D}$$ 의 법선 성분은 연속이다. 그러나 $$\mathbf{D}=\varepsilon\mathbf{E}$$ 이므로 유전율이 다르면 $$\mathbf{E}$$ 의 법선 성분은 불연속일 수 있다. 이 불연속의 물리적 원인은 표면 분극 전하이다.

**접선 성분**: 정전계에서는 $$E_{1t}=E_{2t}$$, 접선 전기장은 연속이다. 도체 표면에서는 정전 평형 조건 때문에 $$E_t=0$$ 이다.

## 커패시턴스

커패시턴스는 전위차당 저장되는 전하량이다.

$$
C = Q/V
$$

기하 구조와 매질의 유전율이 커패시턴스를 결정한다. 평행판 커패시터에서는 fringing field를 무시하면

$$
C = \varepsilon A/d
$$

유전체를 넣으면 전기장이 분극 때문에 감소하고, 같은 자유전하에 대해 전위차가 작아지므로 커패시턴스가 증가한다.

## 커패시턴스 계산 절차

1. 임의의 자유전하 $$Q$$ 를 가정한다.
2. 대칭성과 가우스 법칙으로 $$\mathbf{D}$$ 를 구한다.
3. $$\mathbf{E}=\mathbf{D}/\varepsilon$$ 로 전기장을 구한다.
4. $$V=-\int\mathbf{E}\cdot d\boldsymbol{l}$$ 로 전위차를 구한다.
5. $$C=Q/V$$ 를 계산한다.

### Worked example — 동축 케이블의 커패시턴스

내부 도체 반지름 $$a$$, 외부 도체 반지름 $$b$$, 사이에 유전율 $$\varepsilon$$ 인 유전체, 길이 $$L$$ 인 동축 케이블. 내부 도체에 자유전하 선밀도 $$\rho_l=Q/L$$ 를 가정하면 [03장](03-electrostatics-1-coulomb-and-gauss.md)의 무한 선전하 결과를 그대로 재사용할 수 있다(원통 대칭):

$$
D_r = \frac{\rho_l}{2\pi r}\ (a<r<b),\qquad E_r = \frac{D_r}{\varepsilon} = \frac{\rho_l}{2\pi\varepsilon r}
$$
$$
V_{ab} = -\int_b^a E_r\,dr = \frac{\rho_l}{2\pi\varepsilon}\ln\frac{b}{a}
\quad\Longrightarrow\quad
C = \frac{Q}{V_{ab}} = \frac{2\pi\varepsilon L}{\ln(b/a)}
$$

**숫자 예**: $$a=1\text{mm}$$, $$b=4\text{mm}$$, $$\varepsilon_r=2.3$$(폴리에틸렌), $$L=1\text{m}$$: $$C = \dfrac{2\pi(2.3\times8.85\times10^{-12})(1)}{\ln4} \approx 92.3\text{ pF}$$. 이 결과는 [05장](05-steady-current-and-resistance.md)에서 같은 기하의 누설 저항 $$R=\ln(b/a)/(2\pi\sigma L)$$ 과 곱하면 $$RC=\varepsilon/\sigma$$ (매질만의 함수, 기하와 무관)가 되는 $$J$$-$$D$$ 이중성의 구체적 사례가 된다.

## 전원 연결 여부가 중요한 예시

커패시터 문제에서는 전원을 연결했는지 끊었는지가 매우 중요하다.

- 전원을 끊은 뒤 판 간격을 바꾸면 $$Q_{free}$$ 가 보존된다.
- 전원을 연결한 상태에서는 $$V$$ 가 고정되고, $$Q_{free}$$ 가 변한다.
- 유전체 삽입 시 전원이 끊겨 있으면 $$E$$ 와 $$V$$ 가 감소하고 $$C$$ 가 증가한다.
- 전원이 연결되어 있으면 $$V$$ 는 고정되고, $$C$$ 증가에 맞춰 $$Q_{free}$$ 가 증가한다.

## 유전체 파괴

선형 관계 $$D=\varepsilon E$$ 는 전기장이 너무 크지 않을 때 성립한다. 전기장이 충분히 커지면 절연체가 갑자기 도체처럼 동작하는 유전체 파괴가 발생한다. 공기의 절연파괴 전기장은 대략 $$3\times10^6\text{ V/m}$$ 이다.

## 정전 에너지

커패시터에 저장되는 에너지는

$$
W = \frac12 QV = \frac12 CV^2 = \frac{Q^2}{2C}
$$

장 관점에서는 에너지 밀도를

$$
w_e = \frac12 \mathbf{E}\cdot\mathbf{D}
$$

로 쓴다. 전하가 있는 곳에 에너지가 있다고 볼 수도 있고, 장이 존재하는 공간에 에너지가 있다고 볼 수도 있다. 실제 계산에서는 문제에 편한 표현을 선택한다.

## 전기력과 가상일

커패시터 판 사이의 힘은 가상일 원리로 구할 수 있다. 판 사이 거리나 겹침 면적이 변할 때 저장 에너지가 어떻게 변하는지 계산하면 힘을 얻는다. 단, 전하 고정 조건과 전압 고정 조건에서 에너지 변화 해석이 달라지므로 조건을 먼저 확인해야 한다.

## 응용

강의는 종양 치료 전기장, 심전도 ECG, 메타물질, 유전체 파괴 등을 응용 예로 연결한다. 핵심은 유전체 내부의 분극과 경계조건이 실제 장 분포를 바꾼다는 점이다.

## 연결 노트

- [정전계 I - 쿨롱 법칙과 가우스 법칙](03-electrostatics-1-coulomb-and-gauss.md)
- [정상 전류와 저항](05-steady-current-and-resistance.md)
- [시간 변화장과 맥스웰 방정식](08-time-varying-fields-and-maxwell.md)

## 복습 질문

- $$\mathbf{D}=\varepsilon_0\mathbf{E}+\mathbf{P}$$ 를 도입하는 이유(묶인 전하를 매번 계산하지 않기 위해)를 설명할 수 있는가?
- 유전체 경계에서 $$D_n$$ 연속, $$E_t$$ 연속인 이유와 $$E_n$$ 이 불연속이 될 수 있는 이유는?
- 동축 케이블 커패시턴스 $$C=2\pi\varepsilon L/\ln(b/a)$$ 를 커패시턴스 계산 절차 5단계로 직접 유도할 수 있는가?
- 전원 연결 여부에 따라 유전체 삽입 시 $$E$$, $$V$$, $$C$$, $$Q$$ 중 무엇이 고정/변화하는지 표로 정리할 수 있는가?
- $$RC=\varepsilon/\sigma$$ 관계가 동축 케이블 예제에서 왜 기하 구조와 무관하게 성립하는가?

{% endraw %}

---

이전: [03. 정전계 I - 쿨롱 법칙과 가우스 법칙](03-electrostatics-1-coulomb-and-gauss.md) · 다음: [05. 정상 전류와 저항](05-steady-current-and-resistance.md)
