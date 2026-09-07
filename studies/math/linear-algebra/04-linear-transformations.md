---
layout: page
title: "04. 선형변환"
permalink: /studies/math/linear-algebra/04-linear-transformations/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Linear_Algebra/lecture_notes/04%20%EC%84%A0%ED%98%95%EB%B3%80%ED%99%98.md)

## 한눈에 보기

이 장은 함수를 벡터공간 사이의 구조 보존 사상으로 바라본다. 선형변환은 덧셈과 스칼라 곱을 보존하는 함수이며, 행렬은 선형변환을 계산하는 구체적인 표현이다.

```text
함수 -> 단사와 전사 -> 선형변환 -> image와 kernel
-> rank-nullity theorem -> 표준기저를 이용한 행렬 표현 -> direct sum
```

## 함수의 기본 용어

함수 $f:A\to B$에서 다음 용어를 구분해야 한다.

- 정의역: 함수가 입력을 받는 집합 $A$
- 공역: 함수값이 들어가도록 지정된 집합 $B$
- 치역: 실제로 함수가 만들어내는 값들의 집합

치역은 항상 공역의 부분집합이다.

## 함수와 함수가 아닌 관계

어떤 대응이 함수가 되려면 정의역의 각 원소가 정확히 하나의 함수값을 가져야 한다. 한 입력이 두 개 이상의 출력으로 연결되면 함수가 아니다.

## 단사

단사는 서로 다른 입력이 서로 다른 출력으로 가는 함수이다.

$$
x_1 \neq x_2 \Rightarrow f(x_1)\neq f(x_2)
$$

동치로 다음처럼 쓸 수 있다.

$$
f(x_1)=f(x_2)\Rightarrow x_1=x_2
$$

선형변환의 경우 단사는 kernel이 영벡터만 포함하는지와 연결된다.

## 전사

함수 $f:A\to B$가 전사라는 것은 공역 $B$의 모든 원소가 적어도 한 번은 함수값으로 나온다는 뜻이다.

$$
\forall b\in B,\ \exists a\in A \text{ such that } f(a)=b
$$

예를 들어 $f:\mathbb{R}\to\mathbb{R}$, $f(x)=2x+1$은 전사이다. 모든 실수 $b$에 대해 $x=(b-1)/2$를 고르면 된다.

반면 $f:\mathbb{R}\to\mathbb{R}$, $f(x)=x^2$는 전사가 아니다. 음수는 함수값으로 나오지 않는다.

## 유한체에서의 예시

$\mathbb{Z}_5=\{0,1,2,3,4\}$에서

$$
f(x)=2x \mod 5
$$

를 생각할 수 있다. 2는 5와 서로소이므로 곱셈이 순열처럼 작동한다. 따라서 서로 다른 입력은 서로 다른 출력으로 가고, 전체 원소가 모두 한 번씩 등장한다. 이 함수는 단사이면서 전사이다.

## 선형변환

같은 체 $F$ 위의 벡터공간 $V$, $W$에 대해 함수 $f:V\to W$가 다음을 만족하면 선형변환이다.

$$
f(x+y)=f(x)+f(y)
$$

$$
f(ax)=af(x)
$$

두 조건은 선형결합 보존으로 합칠 수 있다.

$$
f(ax+by)=af(x)+bf(y)
$$

선형변환은 homomorphism이라고도 부른다.

## 관련 용어

- injection: 단사
- surjection: 전사
- bijection: 단사이면서 전사
- isomorphism: 단사, 전사인 선형변환
- automorphism: $V$에서 $V$로 가는 isomorphism
- linear functional: 공역이 체 $F$인 선형변환

## Linear functional

다음 함수는 $\mathbb{R}^2$에서 $\mathbb{R}$로 가는 선형함수이다.

$$
f
\left(
\begin{bmatrix}
x\\y
\end{bmatrix}
\right)
=x+y
$$

행렬 곱으로 쓰면 다음과 같다.

$$
f
\left(
\begin{bmatrix}
x\\y
\end{bmatrix}
\right)
=
\begin{bmatrix}
1 & 1
\end{bmatrix}
\begin{bmatrix}
x\\y
\end{bmatrix}
$$

이 함수는 선형이지만 단사는 아니다. 예를 들어 $(1,0)$과 $(0,1)$이 모두 1로 간다.

## 선형변환의 예시

### Stretching

벡터의 길이를 특정 방향으로 늘리거나 줄이는 변환이다.

예를 들어 $x$축 방향은 그대로 두고 $y$축 방향을 $c$배 하는 변환은 다음처럼 쓸 수 있다.

$$
\begin{bmatrix}
x\\y
\end{bmatrix}
\mapsto
\begin{bmatrix}
1 & 0\\
0 & c
\end{bmatrix}
\begin{bmatrix}
x\\y
\end{bmatrix}
$$

### Reflection

반사는 특정 축이나 직선을 기준으로 벡터를 뒤집는 변환이다. 적절한 행렬을 곱해 표현할 수 있다.

예를 들어 $x$축 반사는 다음이다.

$$
\begin{bmatrix}
x\\y
\end{bmatrix}
\mapsto
\begin{bmatrix}
1 & 0\\
0 & -1
\end{bmatrix}
\begin{bmatrix}
x\\y
\end{bmatrix}
$$

### Rotation

각도 $\theta$만큼 회전시키는 변환은 다음 행렬로 표현된다.

$$
R_\theta =
\begin{bmatrix}
\cos\theta & -\sin\theta\\
\sin\theta & \cos\theta
\end{bmatrix}
$$

따라서

$$
\begin{bmatrix}
s\\t
\end{bmatrix}
=
R_\theta
\begin{bmatrix}
x\\y
\end{bmatrix}
$$

이다.

## Image와 kernel

선형변환 $f:V\to W$에 대해 중요한 부분공간은 두 개이다.

### Image

$$
\text{Im}(f)=\{w\in W : w=f(v),\ v\in V\}
$$

image는 $W$의 부분공간이다. 선형변환이 덧셈과 스칼라 곱을 보존하므로 image 안의 두 벡터를 더하거나 스칼라배해도 여전히 image 안에 있다.

### Kernel

$$
\text{Ker}(f)=\{v\in V : f(v)=0\}
$$

kernel은 $V$의 부분공간이다. $f(v_1)=0$, $f(v_2)=0$이면

$$
f(v_1+v_2)=f(v_1)+f(v_2)=0
$$

이고, 스칼라 $a$에 대해

$$
f(av_1)=af(v_1)=0
$$

이기 때문이다.

## 선형변환의 rank-nullity theorem

선형변환 $f:V\to W$에 대해

$$
\text{rank}(f)=\dim(\text{Im}(f))
$$

$$
\text{nullity}(f)=\dim(\text{Ker}(f))
$$

로 정의하면 다음이 성립한다.

$$
\dim(V)=\text{rank}(f)+\text{nullity}(f)
$$

이는 행렬에서의 rank-nullity theorem과 같은 내용이다. 입력 공간의 차원은 출력으로 살아남는 방향과 kernel로 사라지는 방향으로 나뉜다.

## Rank-nullity theorem의 증명 구조

증명의 핵심 아이디어는 다음과 같다.

1. image의 기저를 $w_1,\ldots,w_s$로 잡는다.
2. 각 $w_i$에 대해 $f(v_i)=w_i$가 되는 $v_i$를 고른다.
3. kernel의 기저를 $u_1,\ldots,u_t$로 잡는다.
4. $\{v_1,\ldots,v_s,u_1,\ldots,u_t\}$가 $V$의 기저임을 보인다.

임의의 $v\in V$에 대해 $f(v)$는 image에 있으므로

$$
f(v)=\sum_{i=1}^{s}a_i w_i
=
f\left(\sum_{i=1}^{s}a_i v_i\right)
$$

이다. 따라서

$$
v-\sum_{i=1}^{s}a_i v_i \in \text{Ker}(f)
$$

이고, 이는 kernel 기저의 선형결합으로 표현된다. 그러므로 전체 집합이 $V$를 span한다.

또 선형독립성은 image 기저와 kernel 기저의 선형독립성을 이용해 보인다.

## 예시: $\mathbb{R}^3$에서 $\mathbb{R}^4$로 가는 변환

다음 변환을 생각한다.

$$
f
\left(
\begin{bmatrix}
x\\y\\z
\end{bmatrix}
\right)
=
\begin{bmatrix}
x\\x\\y\\y
\end{bmatrix}
$$

이 변환은 함수이고 선형변환이다.

그러나 단사는 아니다. $z$값이 달라도 출력이 같기 때문이다.

$$
f
\left(
\begin{bmatrix}
x\\y\\z_1
\end{bmatrix}
\right)
=
f
\left(
\begin{bmatrix}
x\\y\\z_2
\end{bmatrix}
\right)
$$

또 전사도 아니다. 예를 들어 네 성분이 $(1,2,3,3)$처럼 첫 두 성분이 같지 않은 벡터는 image에 들어가지 않는다.

## 선형변환의 행렬 표현

위 변환은 행렬로 다음처럼 표현된다.

$$
A
\begin{bmatrix}
x\\y\\z
\end{bmatrix}
=
\begin{bmatrix}
x\\x\\y\\y
\end{bmatrix}
$$

표준기저 $e_1,e_2,e_3$에 대해

$$
A=[f(e_1)\ f(e_2)\ f(e_3)]
$$

이므로

$$
A=
\begin{bmatrix}
1 & 0 & 0\\
1 & 0 & 0\\
0 & 1 & 0\\
0 & 1 & 0
\end{bmatrix}
$$

이다.

이 행렬의 image 차원은 2이고 kernel 차원은 1이다.

$$
\text{Ker}(f)
=
\left\{
z
\begin{bmatrix}
0\\0\\1
\end{bmatrix}
: z\in\mathbb{R}
\right\}
$$

따라서

$$
\dim(V)=3=2+1
$$

로 rank-nullity theorem이 확인된다.

## 표준기저로 행렬을 찾는 일반 방법

선형변환 $f:\mathbb{R}^n\to\mathbb{R}^m$의 행렬 $A$는 표준기저의 상으로 만든다.

$$
A=[f(e_1)\ f(e_2)\ \cdots\ f(e_n)]
$$

임의의 벡터

$$
x=\sum_{i=1}^{n}a_i e_i
$$

에 대해 선형성 때문에

$$
f(x)=\sum_{i=1}^{n}a_i f(e_i)=Ax
$$

가 된다.

## Direct sum

두 부분공간 $X,Y$에 대해 합공간은 다음과 같다.

$$
X+Y=\{x+y:x\in X,\ y\in Y\}
$$

만약

$$
X\cap Y=\{0\}
$$

이면 $X+Y$를 direct sum이라고 하고

$$
X\oplus Y
$$

로 쓴다.

## Direct sum과 표현의 유일성

$V=X+Y$일 때 다음 두 명제는 동치이다.

$$
V=X\oplus Y
$$

$$
\text{모든 } v\in V \text{는 } v=x+y,\ x\in X,\ y\in Y \text{로 유일하게 표현된다.}
$$

### 왜 교집합이 0이면 유일한가

두 표현이 있다고 하자.

$$
v=x_1+y_1=x_2+y_2
$$

그러면

$$
x_1-x_2=y_2-y_1
$$

왼쪽은 $X$에 있고 오른쪽은 $Y$에 있다. 따라서 이 벡터는 $X\cap Y$에 있다. 교집합이 $\{0\}$이면

$$
x_1=x_2,\quad y_1=y_2
$$

이므로 표현이 유일하다.

### 왜 유일하면 교집합이 0인가

만약 $0$이 아닌 $w\in X\cap Y$가 있으면

$$
v=x+y=(x+w)+(y-w)
$$

처럼 같은 벡터를 두 가지 방식으로 표현할 수 있다. 유일성에 모순이므로 $w=0$이어야 한다.

## 이 장의 핵심 연결

- 행렬은 선형변환의 좌표 표현이다.
- image는 가능한 출력 전체이고, kernel은 0으로 가는 입력 전체이다.
- 단사는 kernel이 작을 때, 전사는 image가 공역 전체일 때 성립한다.
- rank-nullity theorem은 입력 공간을 image에 대응되는 부분과 kernel 부분으로 나눈다.
- direct sum은 벡터 표현의 유일성을 보장하는 공간 분해이다.

## 연결되는 노트

- [선형대수 개요](01-overview.md)
- [행렬과 가우스 소거](02-matrices-and-gaussian-elimination.md)
- [벡터공간과 네 가지 기본 부분공간](03-vector-spaces-and-four-subspaces.md)
- [직교성](05-orthogonality.md)

## 복습 체크리스트

- [ ] 정의역, 공역, 치역의 차이를 설명할 수 있다.
- [ ] 단사, 전사, 전단사의 정의를 말할 수 있다.
- [ ] 선형변환의 두 조건을 확인할 수 있다.
- [ ] image와 kernel이 부분공간인 이유를 설명할 수 있다.
- [ ] 표준기저의 상으로 선형변환 행렬을 구성할 수 있다.
- [ ] 선형변환 버전 rank-nullity theorem을 설명할 수 있다.
- [ ] direct sum과 표현 유일성의 관계를 증명할 수 있다.


---

이전: [03. 벡터공간과 네 가지 기본 부분공간](03-vector-spaces-and-four-subspaces.md) · 다음: [05. 직교성](05-orthogonality.md)
