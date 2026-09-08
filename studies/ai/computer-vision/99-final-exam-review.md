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

{% endraw %}

---

이전: [27. ResNet (project)](27-resnet-project.md)
