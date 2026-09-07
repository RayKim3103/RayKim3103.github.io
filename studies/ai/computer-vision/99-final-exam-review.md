---
layout: page
title: "99. 기말고사 리뷰"
permalink: /studies/ai/computer-vision/99-final-exam-review/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `99` 보강
- 후반부(Transformer · 생성 모델 · 3D 비전) 시험 대비 압축 정리. 막히면 해당 노트로 들어가 복습.

{% raw %}
## 1. Transformer

- 구성: input embedding + **positional encoding** → (LN → MHA → residual → LN → MLP → residual) ×N.
- **scaled dot-product attention**: $\operatorname{softmax}(QK^\top/\sqrt{d_k})\,V$. score $(n\times n)$, 출력 $(n\times d_v)$.
- **multi-head**: 부분공간별로 다른 관계를 병렬 학습.
- **temperature** $1/\tau$: $\tau$↓ → 뾰족(결정적), $\tau$↑ → 평탄.
- **CNN vs Transformer**: CNN은 지역성·가중치공유라는 inductive bias가 있고 receptive field가 층수에 비례; Transformer는 bias가 약하고 1층에서 전역, 대신 데이터가 많이 필요, self-attn $O(n^2)$.
- ViT: 이미지를 패치 토큰으로. Swin: window(+shift) attention으로 $O(n)$ + 계층적.

## 2. 생성 모델 (objective vs sampling)

| | objective | sampling |
|---|---|---|
| Autoregressive | 정확한 NLL, $\prod p(x_i\mid x_{<i})$ | 순차, 느림 |
| VAE | ELBO = 복원 − KL | latent에서 1-step, 흐림 |
| VQ-VAE / VQGAN | 복원 + commitment (+ adversarial) | 2-stage(prior→decoder) |
| GAN | minimax(adversarial), likelihood 없음 | 1-step, 선명, 불안정 |

- ELBO는 $\log p(x)$의 **하한**. KL 항은 근사 사후분포를 prior에 붙잡아 latent를 매끄럽게.
- reparameterization: $z=\mu+\sigma\odot\epsilon$.

## 3. GAN 계열

- value function: $\min_G\max_D \mathbb E[\log D(x)] + \mathbb E[\log(1-D(G(z)))]$; 최적 $D^*=\frac{p_{data}}{p_{data}+p_g}$.
- **DCGAN**(conv 레시피) → **Pix2Pix**(쌍, cGAN+L1+PatchGAN) → **CycleGAN**(비쌍, cycle-consistency) → **PGGAN**(progressive) → **StyleGAN**(AdaIN style 주입, style mixing).
- mode collapse / 불안정 → WGAN, gradient penalty, spectral norm.

## 4. 3D 컴퓨터 비전

- **projection**: $s\,x = K[R\mid t]X$. $K$ intrinsic(5), $[R\mid t]$ extrinsic(6), $P$는 11 DOF. 깊이 $Z$ 소실 → 단일 이미지 스케일 모호.
- **homogeneous coordinate**: 이동·회전·투영을 행렬 곱으로. 무한원점 = 소실점.
- 변환 계층: Euclidean(길이·각) ⊂ similarity(각·비율) ⊂ affine(평행) ⊂ projective(공선성·cross-ratio).
- **calibration**: DLT($A p = 0$, SVD 최소 특이벡터, 정규화 필수) → 비선형 정제; Zhang's method는 평면 타깃 여러 자세.
- **epipolar geometry**: $x'^\top F x = 0$, $l' = Fx$. $F$(7 DOF, uncalibrated), $E = K'^\top F K = [t]_\times R$(5 DOF, calibrated). 8-point + rank-2 강제 + 정규화, RANSAC. → $E$ 분해로 $R,t$ → triangulation → bundle adjustment.

## 자주 나오는 함정

- **$F$ vs $E$ vs $H$**: $F$=uncalibrated 점→선, $E$=calibrated 점→선, $H$=점→점(평면/순수회전).
- **objective ≠ sampling**: 생성 모델은 두 축을 따로 물어본다.
- **attention shape**: $Q,K,V$ 크기와 $QK^\top$의 $(n\times n)$을 그림으로 설명할 수 있어야.
- **$\sqrt{d_k}$** 스케일링, **positional encoding**의 필요성.

## 복습 질문 (자기 점검)

- attention 식을 유도하고 각 행렬의 shape를 말할 수 있는가?
- 4개 생성 계열을 objective/sampling으로 대조할 수 있는가?
- $P = K[R\mid t]$의 DOF와, 단일 이미지로 스케일을 못 구하는 이유는?
- $E = K'^\top F K$를 유도하고 8-point 알고리즘의 단계를 나열할 수 있는가?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **99. 기말고사 리뷰**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 기하/영상처리 주제에서는 좌표계, scale, interpolation, boundary condition을 문제 시작에 명시한다.
- 동차좌표의 scale ambiguity와 실제 pixel 단위 오차를 구분한다.
- 이미지 처리 연산은 필터 모양뿐 아니라 경계 조건, sampling, aliasing, scale 변화까지 함께 봐야 한다.
- 분류, 검출, 분할 모델은 architecture보다 입력 표현, loss, annotation 형식, metric의 차이가 중요하다.
- 3D vision에서는 좌표계, 카메라 모델, homogeneous coordinate, rank/scale ambiguity를 계속 추적해야 한다.

### 문제 풀이 또는 구현 루틴

- 문제를 2D appearance, 3D geometry, learning 문제 중 어디에 가까운지 먼저 분류한다.
- 수식이 나오면 coordinate frame과 tensor shape을 함께 적어 projection과 feature map 흐름을 놓치지 않는다.
- 실험 보고서는 qualitative image와 quantitative metric을 나란히 두고 failure case를 설명한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 시각적으로 좋아 보이는 결과가 metric상 좋은 결과와 항상 일치하지 않는다.
- resize, padding, normalization 같은 preprocessing 차이가 모델 성능을 크게 바꿀 수 있다.
- calibration과 two-view geometry에서는 scale이 정해지지 않는 값이 많아 절대 크기로 해석하면 안 된다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 방법이 조명, viewpoint, scale 변화에 어떻게 반응하는가?
- loss와 metric이 실제 원하는 시각 품질 또는 인식 성능을 잘 대변하는가?
- 실패 사례를 데이터 부족, 모델 capacity, 최적화, annotation noise 중 무엇으로 설명할 수 있는가?
- **99. 기말고사 리뷰**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [27. ResNet (project)](27-resnet-project.md)
