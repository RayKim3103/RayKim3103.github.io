---
layout: page
title: "01. 벡터 대수와 직교 좌표계"
permalink: /studies/math/electromagnetics/01-vector-algebra-and-coordinates/
sitemap: false
---

- **원본 노트**: [GitHub — 01](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electromagnetics/lecture_notes/01%20%EB%B2%A1%ED%84%B0%20%EB%8C%80%EC%88%98%EC%99%80%20%EC%A7%81%EA%B5%90%20%EC%A2%8C%ED%91%9C%EA%B3%84.md) · [02(중복 슬라이드, 병합됨)](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electromagnetics/lecture_notes/02%20%EB%B2%A1%ED%84%B0%20%EB%8C%80%EC%88%98%EC%99%80%20%EC%A7%81%EA%B5%90%20%EC%A2%8C%ED%91%9C%EA%B3%84%20%EC%A4%91%EB%B3%B5%EB%B3%B8.md)

{% raw %}
> 원본은 같은 벡터 해석 슬라이드(내적·외적·직교 좌표계)를 다루는 노트가 01, 02 두 개로 중복 업로드되어 있었습니다. 두 노트의 내용(01의 절차·미소요소, 02의 응용 표·좌표계 선택 기준)을 이 한 파일로 합치고, 좌표 변환 공식과 worked example을 더해 표준 교재(Hayt & Buck, *Engineering Electromagnetics*) 수준으로 보강했습니다.

## 핵심 요약

전자기학은 전기장과 자기장처럼 방향과 크기를 함께 가진 물리량을 다루므로 벡터 해석이 기본 언어가 된다. 이 장은 벡터의 내적과 외적, 세 직교 좌표계(Cartesian·Cylindrical·Spherical), 좌표 변환, 미소 길이·면적·체적 요소를 정리한다. 이후 전기장 적분, 자기장 순환 적분, 플럭스 계산을 하려면 좌표계와 단위벡터가 위치에 따라 어떻게 바뀌는지 정확히 이해해야 한다.

## 벡터와 스칼라

스칼라는 크기만 가진 물리량이고, 벡터는 크기와 방향을 함께 가진 물리량이다. 전위 $$V$$, 온도 $$T$$, 전하밀도 $$\rho$$ 는 스칼라장으로 표현할 수 있고, 전기장 $$\mathbf{E}$$, 자기장 $$\mathbf{B}$$, 전류밀도 $$\mathbf{J}$$ 는 벡터장으로 표현한다.

## 내적

내적은 한 벡터가 다른 벡터 방향으로 얼마나 투영되는지 계산한다.

$$
\mathbf{A} \cdot \mathbf{B} = |\mathbf{A}||\mathbf{B}|\cos\theta = A_xB_x + A_yB_y + A_zB_z
$$

물리적으로 내적은 원하는 방향 성분을 뽑아내는 연산이다. 강의에서는 투영을 Fourier series와 신호 판별의 예로 연결한다. 신호공간에서 수신 벡터를 기준 벡터에 투영하면 어느 심볼에 가까운지 판단할 수 있다. 전자기학에서도 아래 표처럼 특정 경로나 면에 대한 장의 성분을 뽑을 때 내적을 반복해서 사용한다.

| 벡터 연산 | 전자기학 사용 예 |
|---|---|
| $$\mathbf{E}\cdot d\boldsymbol{l}$$ | 전위차, 유도기전력 |
| $$\mathbf{D}\cdot d\mathbf{S}$$ | 전기 플럭스, 가우스 법칙 |
| $$\mathbf{B}\cdot d\mathbf{S}$$ | 자기 플럭스, 패러데이 법칙 |
| $$\mathbf{J}\cdot d\mathbf{S}$$ | 전류, 연속 방정식 |
| $$\mathbf{v}\times\mathbf{B}$$ | 자기력 |
| $$d\boldsymbol{l}\times \mathbf{a}_R$$ | 비오-사바르 법칙 |

## 외적

외적은 두 벡터에 모두 수직인 방향의 벡터를 만든다.

$$
\mathbf{A}\times\mathbf{B} = |\mathbf{A}||\mathbf{B}|\sin\theta\,\mathbf{a}_n
$$

방향은 오른손 법칙으로 정한다. 외적의 대표적인 물리적 의미는 회전과 면적이다.

- $$\mathbf{r}\times\mathbf{F}$$: 토크
- $$\mathbf{v}\times\mathbf{B}$$: 로렌츠 자기력의 방향
- $$d\boldsymbol{l}\times\mathbf{a}_R$$: 비오-사바르 법칙에서 자기장 방향
- 삼중곱 $$\mathbf{A}\cdot(\mathbf{B}\times\mathbf{C})$$: 평행육면체의 부피

## 벡터 연산 worked example

$$\mathbf{A} = (2,-3,1)$$, $$\mathbf{B} = (-1,4,2)$$ 라 하자.

$$
\mathbf{A}\cdot\mathbf{B} = (2)(-1)+(-3)(4)+(1)(2) = -2-12+2 = -12
$$
$$
\mathbf{A}\times\mathbf{B} = \begin{vmatrix}\mathbf{a}_x & \mathbf{a}_y & \mathbf{a}_z \\ 2 & -3 & 1 \\ -1 & 4 & 2\end{vmatrix}
= \mathbf{a}_x(-3\cdot2 - 1\cdot4) - \mathbf{a}_y(2\cdot2-1\cdot(-1)) + \mathbf{a}_z(2\cdot4-(-3)(-1))
= (-10,\,-5,\,5)
$$

검산: $$\mathbf{A}\times\mathbf{B}$$ 는 $$\mathbf{A}$$, $$\mathbf{B}$$ 모두와 수직이어야 하므로 $$\mathbf{A}\cdot(\mathbf{A}\times\mathbf{B}) = 2(-10)+(-3)(-5)+1(5) = -20+15+5=0$$ — 성립. $$\mathbf{A}\cdot\mathbf{B}<0$$ 이므로 두 벡터 사이 각도는 $$90°$$ 보다 크다는 것도 부호만으로 바로 읽을 수 있다.

## 직교 좌표계

직교 좌표계는 한 점이 서로 수직인 세 곡면의 교점으로 표현되는 좌표계이다. 전자기학에서는 문제의 대칭성에 맞춰 좌표계를 선택해야 계산이 단순해진다.

| 좌표계 | 좌표 | 자주 쓰는 문제 |
|---|---|---|
| Cartesian | $$(x,y,z)$$ | 평판, 직육면체, 균일장 |
| Cylindrical | $$(r,\phi,z)$$ | 긴 도선, 원통, 동축 케이블, 솔레노이드 |
| Spherical | $$(R,\theta,\phi)$$ | 점전하, 구, 구대칭 전하분포 |

좌표계를 잘 고르면 장의 방향이 하나의 단위벡터로 고정되고, 적분식이 단순한 대수식으로 줄어든다. 반대로 좌표계를 잘못 고르면 같은 물리를 설명하더라도 불필요한 성분 계산이 늘어난다. 평판 구조이면 Cartesian, 무한 직선 도선·원통 도체·솔레노이드·동축선은 Cylindrical, 점전하·구 도체·구대칭 전하분포는 Spherical이 자연스럽다.

핵심은 원통좌표와 구좌표의 단위벡터가 위치에 따라 방향이 바뀐다는 점이다. 그래서 좌표 변환을 할 때 벡터의 성분뿐 아니라 단위벡터의 방향도 함께 바꾸어야 한다.

## 좌표 변환

**Cartesian ↔ Cylindrical** — 좌표:
$$
r=\sqrt{x^2+y^2},\quad \phi=\tan^{-1}(y/x),\quad z=z
\qquad\Longleftrightarrow\qquad
x=r\cos\phi,\quad y=r\sin\phi,\quad z=z
$$
단위벡터(회전변환, $$\phi$$ 는 그 점에서의 방위각):
$$
\begin{bmatrix}\mathbf{a}_r\\ \mathbf{a}_\phi\\ \mathbf{a}_z\end{bmatrix}
=\begin{bmatrix}\cos\phi & \sin\phi & 0\\ -\sin\phi & \cos\phi & 0\\ 0&0&1\end{bmatrix}
\begin{bmatrix}\mathbf{a}_x\\ \mathbf{a}_y\\ \mathbf{a}_z\end{bmatrix}
$$
벡터 성분도 같은 행렬로 변환된다: $$A_r = A_x\cos\phi+A_y\sin\phi$$, $$A_\phi=-A_x\sin\phi+A_y\cos\phi$$.

**Cartesian ↔ Spherical**:
$$
R=\sqrt{x^2+y^2+z^2},\quad \theta=\cos^{-1}(z/R),\quad \phi=\tan^{-1}(y/x)
\qquad\Longleftrightarrow\qquad
x=R\sin\theta\cos\phi,\ y=R\sin\theta\sin\phi,\ z=R\cos\theta
$$

**worked example**: 점 $$P=(3,4,12)$$(Cartesian, 단위 m)을 변환하면
$$
r=\sqrt{3^2+4^2}=5,\ \phi=\tan^{-1}(4/3)\approx53.1°,\ z=12 \quad\text{(Cylindrical)}
$$
$$
R=\sqrt{3^2+4^2+12^2}=13,\ \theta=\cos^{-1}(12/13)\approx22.6°,\ \phi\approx53.1° \quad\text{(Spherical)}
$$
$$3$$-$$4$$-$$5$$ 직각삼각형(원점→$$xy$$ 투영)과 $$5$$-$$12$$-$$13$$ 직각삼각형(투영→실제 점)이 그대로 $$r$$ 과 $$R$$ 로 나타난다 — 좌표 변환은 결국 피타고라스 정리를 반복 적용하는 것이다.

## 미소 요소

적분을 세우려면 좌표계별 미소 요소를 정확히 써야 한다.

**Cartesian**
$$
d\boldsymbol{l} = \mathbf{a}_x\,dx + \mathbf{a}_y\,dy + \mathbf{a}_z\,dz,\qquad dV = dx\,dy\,dz
$$

**Cylindrical**
$$
d\boldsymbol{l} = \mathbf{a}_r\,dr + \mathbf{a}_\phi\,r\,d\phi + \mathbf{a}_z\,dz
$$
$$
dS(r{=}\text{const}) = \mathbf{a}_r\,r\,d\phi\,dz,\quad dS(\phi{=}\text{const}) = \mathbf{a}_\phi\,dr\,dz,\quad dS(z{=}\text{const}) = \mathbf{a}_z\,r\,dr\,d\phi,\qquad dV = r\,dr\,d\phi\,dz
$$

**Spherical**
$$
d\boldsymbol{l} = \mathbf{a}_R\,dR + \mathbf{a}_\theta\,R\,d\theta + \mathbf{a}_\phi\,R\sin\theta\,d\phi,\qquad dV = R^2\sin\theta\,dR\,d\theta\,d\phi
$$

## 문제 풀이 절차

1. 대칭성을 먼저 확인한다.
2. 대칭에 맞는 좌표계를 고른다.
3. 장의 방향을 단위벡터로 표현한다.
4. 필요한 미소 길이, 면적, 체적 요소를 쓴다.
5. 내적 또는 외적으로 실제 기여 성분만 남긴다.

## 자주 헷갈리는 지점

- 원통좌표의 $$\mathbf{a}_r$$, $$\mathbf{a}_\phi$$ 는 점의 위치가 바뀌면 방향도 바뀐다 — $$\mathbf{a}_\phi$$ 는 $$\mathbf{a}_r$$ 을 그 점에서 $$90°$$ 회전한 접선 방향이다.
- 구좌표의 $$\theta$$ 와 $$\phi$$ 정의를 교재 관례에 맞춰 확인해야 한다(물리 관례: $$\theta$$=극각, 공학 관례와 다를 수 있음).
- 벡터를 좌표 변환할 때 성분만 바꾸는 것이 아니라 단위벡터 기준도 바꾸어야 한다.
- 적분면의 법선 방향을 잘못 잡으면 플럭스 부호가 바뀐다.
- $$\mathbf{A}\cdot\mathbf{B}=0$$ 이면 두 벡터는 서로 수직이고, $$\mathbf{A}\times\mathbf{B}=0$$ 이면 두 벡터는 서로 평행하다.

## 연결 노트

- [강의 개요와 전자기학 지도](00-course-overview.md)
- [벡터 미적분과 장 이론](02-vector-calculus-and-field-theory.md)

## 복습 질문

- $$\mathbf{A}\cdot\mathbf{B}$$ 와 $$\mathbf{A}\times\mathbf{B}$$ 의 기하학적 의미와 각각이 0이 되는 조건은?
- $$\mathbf{A}=(2,-3,1)$$, $$\mathbf{B}=(-1,4,2)$$ 의 외적을 직접 계산하고, $$\mathbf{A}\cdot(\mathbf{A}\times\mathbf{B})=0$$ 임을 검산할 수 있는가?
- Cartesian ↔ Cylindrical 단위벡터 변환 행렬을 쓰고, 왜 $$\phi$$ 에 의존하는지 설명할 수 있는가?
- 점 $$(3,4,12)$$ 를 cylindrical·spherical 좌표로 변환할 수 있는가?
- 어떤 전하분포에 어떤 좌표계를 선택해야 하는지, 그 이유를 대칭성으로 설명할 수 있는가?

{% endraw %}

---

이전: [00. 강의 개요와 전자기학 지도](00-course-overview.md) · 다음: [02. 벡터 미적분과 장 이론](02-vector-calculus-and-field-theory.md)
