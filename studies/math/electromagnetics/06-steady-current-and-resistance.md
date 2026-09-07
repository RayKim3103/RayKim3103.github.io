---
layout: page
title: "06. 정상 전류와 저항"
permalink: /studies/math/electromagnetics/06-steady-current-and-resistance/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electromagnetics/lecture_notes/06%20%EC%A0%95%EC%83%81%20%EC%A0%84%EB%A5%98%EC%99%80%20%EC%A0%80%ED%95%AD.md)

## 핵심 요약

정상 전류장은 움직이는 자유전하가 만드는 전류밀도 `J`를 다룬다. 미시적으로는 전자의 열운동과 충돌, 외부 전기장에 의한 drift velocity가 옴의 법칙으로 이어진다. 거시적으로는 연속 방정식, KCL, KVL, 저항, 전력 손실, 도체와 유전체의 이중성이 핵심이다.

## 전류의 종류

강의는 전류를 다음처럼 구분한다.

- Conduction current: 금속이나 반도체에서 전자와 정공의 drift 운동
- Convection current: 진공 중 전자 빔처럼 매질 없이 움직이는 전하
- Electrolytic current: 전해질에서 양이온과 음이온의 이동
- Displacement current: 시간 변화 전기 플럭스에 대응하는 전류 성분

정상 전류 장에서는 주로 conduction current를 다룬다.

## 전류밀도 J

전류밀도는 단위 면적당 흐르는 전류이다.

```text
I = ∫S J · dS
```

1차원 도선 전류는 `I [A]`, 3차원 부피 전류는 `J [A/m^2]`, 2차원 표면 전류는 `Js [A/m]`로 구분한다.

전자 농도 `nc`, 전하량 `q`, 평균 drift velocity `vd`를 쓰면

```text
J = nc q vd
```

로 표현된다. 전자의 열속도는 매우 크지만 무작위라 평균 전류를 만들지 않고, 외부 전기장이 만든 작은 drift 성분이 실제 전류를 만든다.

## 점 형태의 옴의 법칙

충돌 때문에 전자는 계속 가속되지 않고 평균 drift velocity가 전기장에 비례한다. 이 관계가 거시적으로 전도도 `σ`를 만든다.

```text
J = σE
E = ρc J
```

여기서 `σ`는 전도도이고 `ρc = 1/σ`는 비저항이다. 금속은 `σ`가 크고, 고무나 석영은 매우 작다. 반도체의 전도도는 온도와 carrier 농도에 크게 의존한다.

## 저항

도체의 두 단자 전위차와 전류의 비를 저항이라 한다.

```text
R = V / I
```

균일한 단면적 `A`, 길이 `l`, 전도도 `σ`를 가진 도체에서는

```text
R = l / (σA)
```

일반 구조에서는 다음 절차로 구한다.

1. 두 단자 사이 전위차 `V`를 가정한다.
2. 도체 내부 전기장 `E`를 구한다.
3. `J = σE`로 전류밀도를 구한다.
4. `I = ∫ J · dS`로 전체 전류를 구한다.
5. `R = V / I`를 계산한다.

## 연속 방정식

전하 보존은 연속 방정식으로 표현된다.

```text
∇ · J = -∂ρ/∂t
```

정상 전류에서는 시간에 따른 전하 축적이 없으므로

```text
∇ · J = 0
```

이다. 즉 정상 전류선은 내부에서 시작하거나 끝나지 않는다. 회로 이론의 KCL은 이 식을 접합부에 적용한 결과이다.

## KCL과 KVL

정상 상태에서 임의의 닫힌 면을 통과하는 총 전류는 0이다.

```text
Σ Ij = 0
```

이것이 KCL이다. 한편 정전기장만으로는 닫힌 회로에 지속적인 전류를 유지할 수 없다. 정전기장은 보존장이므로

```text
∮ E · dl = 0
```

이고, 저항에서 에너지를 잃은 전하를 다시 올려줄 비보존 에너지 source가 필요하다. 배터리나 발전기가 제공하는 기전력 `Vemf`를 포함하면 회로의 KVL 형태가 나온다.

```text
Vemf = IR
```

## 전하 완화 시간

도체 내부에 부피 전하가 생기면 빠르게 재분포한다. 완화 시간은

```text
τ = ε / σ
```

로 주어지며, 물질이 도체처럼 동작하는지 절연체처럼 동작하는지 판단하는 척도이다. 금속에서는 극도로 짧고, 석영 같은 절연체에서는 매우 길다.

## 전력 손실

전기장이 carrier에 일을 하고, 그 에너지는 충돌을 통해 열로 바뀐다. 점 형태의 Joule 법칙은

```text
p = E · J
P = ∫V E · J dV
```

이다. 균일한 도체에서는 익숙한 회로식과 연결된다.

```text
P = VI = I^2 R = V^2 / R
```

## 경계조건

정상 전류 경계에서는 전류밀도의 법선 성분이 연속이다.

```text
J1n = J2n
```

전기장의 접선 성분은 정전계 관계에서 연속이고, `J = σE`이므로 전도도가 다른 두 매질에서는 `J`의 접선 성분 비가 전도도 비를 따른다. 완전 절연체와 접한 도체 표면에서는 법선 전류가 0이 되어 전류가 표면을 따라 흐른다.

## J와 D의 이중성

정상 전류 문제와 유전체 커패시턴스 문제는 수학적 구조가 비슷하다.

| 도전 매질 | 유전체 |
| --- | --- |
| `J = σE` | `D = εE` |
| `∇ · J = 0` | `∇ · D = ρfree` |
| `R = V/I` | `C = Q/V` |

같은 기하 구조에서 저항을 알면 커패시턴스를 유추할 수 있는 경우가 있다. 대표적으로 `RC = ε/σ` 형태의 관계가 등장한다.

## 연결 노트

- [정전계 II - 유전체와 커패시턴스](05-electrostatics-2-dielectrics-and-capacitance.md)
- [정자계 I - 비오-사바르와 앙페르 법칙](07-magnetostatics-1-biot-savart-and-ampere.md)
- [시간 변화장과 맥스웰 방정식](09-time-varying-fields-and-maxwell.md)


---

이전: [05. 정전계 II - 유전체와 커패시턴스](05-electrostatics-2-dielectrics-and-capacitance.md) · 다음: [07. 정자계 I - 비오-사바르와 앙페르 법칙](07-magnetostatics-1-biot-savart-and-ampere.md)
