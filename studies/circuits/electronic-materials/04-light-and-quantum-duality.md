---
layout: page
title: "04. 빛과 양자 이중성 (Light & Quantum Duality)"
permalink: /studies/circuits/electronic-materials/04-light-and-quantum-duality/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes) · 교재: Kasap Ch.3
- 이전: [03. 결정질·비정질·공정](03-crystalline-amorphous-and-fabrication.md) · 다음: [05. 터널링·흑체·레이저](05-tunneling-blackbody-laser.md)

{% raw %}
## 개요

```text
빛의 파동성(회절·간섭·결맞음) ↔ 입자성(광전효과·Compton)
전자의 파동성(이중슬릿) → 파동함수 ψ(x), |ψ|² 확률밀도 → 무한 우물 에너지 양자화
```

---

## 1. 빛의 회절

**회절** = 파동이 장애물/좁은 틈을 지나 직진 경로 뒤로 퍼짐. 슬릿 폭 $$a$$가 파장 $$\lambda$$에 가까울수록 회절 큼.

| 영역 | 조건 |
|---|---|
| Kirchhoff | 슬릿 근처 일반 회절 |
| Fresnel | 근거리 회절 |
| **Fraunhofer** | $$z \gg \pi a^2/\lambda$$ — 원거리, 평면파 근사 |

### Fraunhofer 단일 슬릿
어두운 무늬:
$$
a\sin\theta_n = n\lambda \;\Rightarrow\; \theta_n \approx \frac{n\lambda}{a}\;(\text{소각})
$$
$$a$$ 작을수록, $$\lambda$$ 길수록 회절각 ↑.

## 2. 간섭과 결맞음

$$
\text{보강: 경로차} = n\lambda, \qquad \text{상쇄: 경로차} = (n + \tfrac12)\lambda
$$

**결맞음(coherence)**: 두 광원이 같은 주파수 + 일정한 위상 관계 → stationary interference pattern 가능. laser = coherent, 태양·lamp = incoherent.

## 3. 빛의 전자기파 관점

빛 = 시간 변화하는 $$\vec{E} \perp \vec{B}$$, 둘 다 진행 방향에 수직인 **횡파**. 회절·간섭·편광·X-ray diffraction 설명. 결정에 X-ray 조사 → 원자면 반사파의 특정 방향 보강간섭 → diffraction spot/ring (**Bragg**: $$2d\sin\theta = n\lambda$$).

## 4. Young 이중슬릿

$$
\text{보강: } S_1P - S_2P = n\lambda, \qquad \text{상쇄: } (n + \tfrac12)\lambda
$$
실제 무늬 = 단일 슬릿 회절 **envelope** × 이중슬릿 간섭무늬.

## 5. 광전효과

| 실험 결과 | 의미 |
|---|---|
| 세기 ↑ → 포화 전류 ↑ | 방출 전자 수 ∝ 광자 수 |
| **최대 운동에너지는 세기가 아니라 주파수로 결정** | 광자 1개 = 전자 1개 (양자) |
| 금속마다 threshold frequency $$\nu_0$$ 다름 | work function $$\Phi$$ 차이 |

$$
KE_{max} = h\nu - h\nu_0 = h\nu - \Phi
$$
$$\nu < \nu_0$$ 이면 세기가 아무리 커도 방출 없음 → **빛 에너지는 $$h\nu$$ 단위로 전달** (입자성).

정지 전압(stopping voltage) $$V_0$$: $$eV_0 = KE_{max}$$.

## 6. Compton 산란

X-ray 광자가 거의 자유로운 전자와 충돌 → 산란 광자 **파장 증가**:
$$
\Delta\lambda = \frac{h}{m_e c}(1 - \cos\theta)
$$
($$h/m_e c = 2.43\,\text{pm}$$ = Compton 파장.) 광전효과가 에너지 양자화라면 Compton은 **광자 운동량**의 실재성.

## 7. 전자의 파동성

전자를 가속해 이중슬릿 통과 → 스크린에 **간섭무늬**. 전자는 한 점에서 검출되지만 누적하면 파동 간섭 분포. de Broglie: $$\lambda = h/p$$. → 전자 상태를 궤적이 아니라 **파동함수**로.

## 8. 파동함수와 무한 퍼텐셜 우물

### ψ(x) 조건
물리적으로 허용되려면 유한 · 단일값 · 정규화 가능 · 연속. 발산·불연속 함수 불가.

### 확률 해석
$$
\text{probability density} = |\psi(x)|^2, \qquad \int |\psi|^2\,dx = 1
$$

### 1D 무한 우물 (폭 $$L$$)
경계에서 $$\psi = 0$$ → 정상파 조건 → 에너지 양자화:
$$
\psi_n(x) = \sqrt{\frac{2}{L}}\sin\frac{n\pi x}{L}, \qquad
E_n = \frac{n^2 \pi^2 \hbar^2}{2mL^2} = \frac{n^2 h^2}{8mL^2}
$$
낮은 $$n$$일수록 node 적고 에너지 낮음. (양자점·박막의 quantum confinement 기초.)

---

## 복습 질문

- 단일 슬릿 회절각이 $$\lambda/a$$에 비례하는 이유, Fraunhofer 조건은?
- 광전효과에서 세기·주파수가 각각 포화 전류·최대 운동에너지에 주는 영향, $$KE_{max} = h\nu - \Phi$$의 의미는?
- Compton 산란이 광자의 무엇을 보여주는가? $$\Delta\lambda$$가 산란각에 어떻게 의존하나?
- 전자 이중슬릿 실험이 전자의 파동성을 보여주는 방식은?
- 무한 우물에서 $$E_n \propto n^2/L^2$$이 나오는 과정(경계조건)은?
{% endraw %}

---

이전: [03. 결정질·비정질·공정](03-crystalline-amorphous-and-fabrication.md) · 다음: [05. 터널링·흑체·레이저](05-tunneling-blackbody-laser.md)
