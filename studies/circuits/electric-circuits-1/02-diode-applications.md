---
layout: page
title: "02. 다이오드 응용"
permalink: /studies/circuits/electric-circuits-1/02-diode-applications/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/02%20%EB%8B%A4%EC%9D%B4%EC%98%A4%EB%93%9C%20%EC%9D%91%EC%9A%A9.md)

## 한눈에 보기

이 장은 다이오드의 회로 응용을 다룬다. 핵심은 다이오드를 어떤 모델로 볼 것인지 정하고, 켜짐/꺼짐 조건에 따라 입력-출력 관계를 조각별로 해석하는 것이다.

```text
지수 모델 -> constant voltage model -> ideal diode model
-> clipping/limiting -> half-wave rectifier -> full-wave rectifier
-> capacitor smoothing
```

## 다이오드 전류식 복습

PN 접합 다이오드의 전류는 다음으로 표현된다.

$$
I=I_S(e^{V_D/V_T}-1)
$$

순방향 전압이 충분히 크면

$$
I\approx I_S e^{V_D/V_T}
$$

로 근사한다.

하지만 회로 응용에서는 계산을 단순화하기 위해 다음 모델을 자주 쓴다.

## 다이오드 근사 모델

### Ideal diode model

이상적인 다이오드는 다음처럼 동작한다.

- 켜짐: 단락, $V_D=0$
- 꺼짐: 개방, $I_D=0$

켜짐 기준 전압을 0V로 둔다.

### Constant voltage model

실제 Si 다이오드는 켜졌을 때 대략 $0.7V$에서 $0.8V$ 정도의 전압강하를 갖는다.

- 켜짐: $V_D=V_{D,on}$
- 꺼짐: $I_D=0$

회로의 정확도와 복잡도 사이 타협으로 많이 사용한다.

## 다이오드 회로 해석 절차

1. 다이오드가 켜졌다고 가정한다.
2. 해당 모델에 맞게 다이오드를 단락 또는 전압원으로 치환한다.
3. 회로 방정식을 풀어 전류 방향과 전압 조건을 확인한다.
4. 가정한 켜짐 조건이 맞으면 해를 채택한다.
5. 맞지 않으면 꺼짐 상태로 다시 해석한다.

다이오드 회로는 조각별 선형 회로로 보는 것이 핵심이다.

## 양의 반주기 통과 회로

입력에 직렬 다이오드가 있고 출력이 저항에 걸리는 회로를 생각하면, 다이오드는 입력의 양의 부분만 통과시킨다.

ideal model에서는

$$
V_{out}=
\begin{cases}
V_{in}, & V_{in}>0\\
0, & V_{in}\le 0
\end{cases}
$$

constant voltage model에서는

$$
V_{out}=
\begin{cases}
V_{in}-V_{D,on}, & V_{in}>V_{D,on}\\
0, & V_{in}\le V_{D,on}
\end{cases}
$$

이다.

## Shunt clipping 회로

입력과 직렬 저항 뒤 출력 노드가 있고, 출력 노드에서 다이오드가 접지로 연결된 회로에서는 출력이 특정 전압 이상으로 올라가지 못한다.

다이오드가 켜지면 출력은 거의

$$
V_{out}\approx V_{D,on}
$$

으로 제한된다. 입력이 더 커져도 남는 전압은 직렬 저항에 걸린다.

이 회로는 clipping 또는 limiting 동작을 한다.

## 저항 분배가 포함된 클리퍼

출력 노드에 저항 분배기와 다이오드가 함께 있으면, 다이오드가 꺼진 구간에서는 단순 분압기로 동작한다.

예를 들어 $R_1$, $R_2$가 분압기를 이루면 ideal 상태에서

$$
V_{out}=\frac{R_2}{R_1+R_2}V_{in}
$$

이다.

다이오드가 켜지는 순간 이후에는 출력이 $V_{D,on}$ 근처로 제한되거나, 회로 구조에 따라 기울기가 달라진다.

## 기울기가 바뀌는 piecewise transfer curve

다이오드 응용 회로의 $V_{out}$ 대 $V_{in}$ 그래프는 보통 조각별 선형이다.

- 다이오드 off: 저항망만 남으므로 한 기울기
- 다이오드 on: 다이오드 전압 조건이 추가되어 다른 기울기
- ideal model: 임계점이 0V
- constant voltage model: 임계점이 $V_{D,on}$

회로 해석에서는 시간파형과 전달특성 그래프를 함께 그리면 실수를 줄일 수 있다.

## 정류기

정류기는 AC를 DC에 가까운 형태로 바꾸는 회로이다.

완벽한 DC를 즉시 얻는 것은 아니지만, 음의 반주기를 제거하거나 뒤집고, 커패시터로 리플을 줄여 DC 전원처럼 사용할 수 있다.

## 반파 정류기

반파 정류기는 입력의 한쪽 반주기만 통과시킨다.

ideal diode에서는 양의 반주기 동안

$$
V_{out}=V_{in}
$$

이고 음의 반주기 동안

$$
V_{out}=0
$$

이다.

constant voltage model에서는 양의 반주기에서도 약 $V_{D,on}$만큼 출력이 낮아진다.

$$
V_{out}=V_{in}-V_{D,on}
$$

## 커패시터 필터

정류기 출력에 커패시터를 병렬로 연결하면 출력이 더 평탄해진다.

동작은 다음과 같다.

- 입력이 상승하여 다이오드가 켜지면 커패시터가 충전된다.
- 입력이 내려가 다이오드가 꺼지면 커패시터가 부하저항을 통해 방전된다.
- 커패시턴스가 크면 방전이 느려져 리플이 작아진다.
- 커패시턴스가 작으면 리플이 커진다.

완벽한 DC는 아니지만 DC 전원 역할을 할 수 있는 파형이 만들어진다.

## 전파 정류기

전파 정류기는 입력의 양의 반주기와 음의 반주기를 모두 양의 출력으로 바꾼다.

브리지 정류기에서는 입력 극성에 따라 서로 다른 두 개의 다이오드가 켜지고 나머지 두 개는 꺼진다.

ideal model에서는

$$
V_{out}=|V_{in}|
$$

이다.

constant voltage model에서는 한 경로에 다이오드 두 개가 직렬로 들어가므로

$$
V_{out}\approx |V_{in}|-2V_{D,on}
$$

이 된다.

## 회로 해석에서 자주 생기는 실수

- 다이오드 방향과 전류 방향을 혼동한다.
- ideal model과 constant voltage model의 임계전압을 섞어 쓴다.
- 다이오드가 켜진 후 출력이 무조건 입력과 같다고 생각한다.
- 커패시터 필터에서 충전 구간과 방전 구간을 구분하지 않는다.
- 전파 정류기에서 도통하는 다이오드 개수를 빠뜨린다.

## 핵심 정리

- 다이오드 회로는 on/off 가정 후 검증하는 방식으로 푼다.
- ideal model은 빠른 정성 해석에 좋다.
- constant voltage model은 실제 Si 다이오드의 전압강하를 반영한다.
- 클리퍼는 출력 전압을 일정 수준으로 제한한다.
- 반파 정류기는 한쪽 반주기만 사용한다.
- 전파 정류기는 양쪽 반주기를 모두 같은 극성으로 만든다.
- 커패시터는 정류 출력의 리플을 줄인다.

## 연결되는 노트

- [반도체와 PN 접합](01-semiconductors-and-pn-junction.md)
- [BJT 기본 동작](03-bjt-operation.md)

## 복습 체크리스트

- [ ] ideal diode model과 constant voltage model을 구분할 수 있다.
- [ ] 다이오드 on/off 가정 검증 절차로 회로를 풀 수 있다.
- [ ] 클리퍼 회로의 전달특성을 그릴 수 있다.
- [ ] 반파 정류기와 전파 정류기의 출력 파형을 비교할 수 있다.
- [ ] 커패시터 필터가 리플을 줄이는 이유를 설명할 수 있다.


---

이전: [01. 반도체와 PN 접합](01-semiconductors-and-pn-junction.md) · 다음: [03. BJT 기본 동작](03-bjt-operation.md)
