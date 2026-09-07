---
layout: page
title: "10. Generative Adversarial Networks"
permalink: /studies/ai/computer-vision/10-generative-adversarial-networks/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `10` 보강
- 표준 자료(GAN·DCGAN·pix2pix·CycleGAN·StyleGAN 논문) 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

GAN은 두 네트워크의 게임으로 학습한다. **generator** $G$는 노이즈 $z$에서 가짜 이미지를 만들고, **discriminator** $D$는 진짜/가짜를 구분한다. $G$는 $D$를 속이도록, $D$는 안 속도록 번갈아 학습.

## 1. Adversarial objective

$$
\min_G \max_D \;\;
\mathbb{E}_{x\sim p_{\text{data}}}[\log D(x)]
+ \mathbb{E}_{z\sim p_z}[\log(1 - D(G(z)))]
$$

- 최적 $D^*(x) = \dfrac{p_{\text{data}}(x)}{p_{\text{data}}(x)+p_g(x)}$ → 이때 $G$ 목적은 $\mathrm{JS}(p_{\text{data}}\Vert p_g)$ 최소화.
- 실무: $G$는 $\log(1-D(G(z)))$ 대신 $-\log D(G(z))$를 최대화(초기 gradient가 강함, "non-saturating loss").
- **명시적 likelihood가 없다** — 표본 품질은 좋지만 밀도 평가는 불가.

## 2. 학습이 어려운 이유

- **불안정**: 두 손실이 동시에 안 내려감. $D$가 너무 강하면 $G$ gradient 소실, $G$가 너무 강하면 $D$가 무너짐.
- **mode collapse**: $G$가 몇 개 표본만 반복 생성(다양성 상실).
- 완화책: 균형 잡힌 학습률/스텝, spectral norm, gradient penalty, **WGAN**(Wasserstein 거리 + Lipschitz 제약), 다양한 정규화, EMA of $G$ weights.

## 3. 발전 흐름

| 모델 | 아이디어 |
|---|---|
| **DCGAN** | $G$/$D$를 conv/transposed-conv로, BatchNorm, 안정적 학습 레시피. latent 공간 산술(벡터 연산)이 의미를 가짐 |
| **Pix2Pix** | **쌍 데이터** image-to-image 변환. conditional GAN + L1 loss + PatchGAN discriminator |
| **CycleGAN** | **비쌍(unpaired)** 변환. cycle-consistency $F(G(x))\approx x$ 로 대응 없이 도메인 변환(말↔얼룩말) |
| **PGGAN** | 저해상도부터 점진적으로 층을 추가(progressive growing) → 고해상도 안정화 |
| **StyleGAN** | latent를 style로 각 해상도에 주입(AdaIN), noise 입력으로 stochastic detail, **style mixing**으로 속성 분리·제어 |

## 4. VAE / diffusion과의 대비

- VAE: 안정적 학습, likelihood 하한, 흐린 표본.
- GAN: 불안정, likelihood 없음, 선명한 표본.
- diffusion: 안정적, 반복 샘플링(느림), 현재 최고 품질 — GAN을 상당 부분 대체.

## 자주 틀리는 지점

- $G$의 "non-saturating" 손실($-\log D(G(z))$)과 원 minimax의 $G$ 항을 헷갈리지 말 것 — gradient 거동이 다르다.
- Pix2Pix는 **쌍** 데이터, CycleGAN은 **비쌍** — cycle-consistency가 대응 부재를 메운다.
- mode collapse는 loss가 낮아 보여도 발생한다(다양성 지표·FID를 따로 확인).
- GAN 품질↑는 adversarial loss 덕분이며 likelihood와 무관.

## 복습 질문

- GAN의 value function을 쓰고 최적 $D$와 그때 $G$가 최소화하는 대상을 유도하라.
- non-saturating generator loss를 쓰는 이유는?
- Pix2Pix와 CycleGAN의 데이터 요건과 핵심 loss 차이는?
- StyleGAN이 속성을 분리·제어하는 메커니즘(AdaIN, style mixing)은?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **10. Generative Adversarial Networks**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 생성모델은 likelihood, adversarial objective, reconstruction/perceptual loss가 각각 다른 품질 기준을 최적화한다.
- 샘플 이미지는 mode collapse, blur, texture artifact, conditioning failure를 따로 보며 해석한다.
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
- **10. Generative Adversarial Networks**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [09. Autoregressive Models & VAE](09-autoregressive-and-vae.md) · 다음: [11. 3D Geometry](11-3d-geometry.md)
