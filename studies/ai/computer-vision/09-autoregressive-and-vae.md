---
layout: page
title: "09. Autoregressive Models & VAE"
permalink: /studies/ai/computer-vision/09-autoregressive-and-vae/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `09` + `09R(개정판)` + `09R2(2차 개정판)` 통합·보강
- 표준 자료(CS231n · VAE/VQ-VAE/VQGAN 논문) 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

두 가지 명시적 생성 방식을 비교한다. **Autoregressive**는 likelihood를 순차적으로 factorization한다. **VAE 계열**은 잠재변수를 통해 데이터를 생성하고 하한(ELBO)을 최적화한다. VQ-VAE·VQGAN은 이산 잠재 + 별도 prior + (선택적) adversarial loss로 고품질 생성을 만든다.

## 1. Autoregressive models

체인 룰로 결합확률을 분해:
$$
p(x) = \prod_{i=1}^{n} p(x_i \mid x_1,\dots,x_{i-1})
$$

- 학습: **teacher forcing** — 실제 이전 토큰을 조건으로 주고 cross-entropy(= NLL) 최소화. 병렬 학습 가능.
- 생성: 모델 출력을 하나씩 다시 입력 → **순차**라서 느림.
- **PixelRNN / PixelCNN**: 픽셀을 좌상→우하 순서로 생성. PixelCNN은 masked conv로 미래 픽셀 차단.
- **Image Transformer**: 이미지를 토큰 시퀀스로 보되 **2D local attention**으로 $O(n^2)$ 비용을 제한.

장점: 정확한 likelihood, 안정적 학습, 고품질. 단점: 느린 샘플링.

## 2. Variational Autoencoder (VAE)

$$
p_\theta(x) = \int p_\theta(x\mid z)\,p(z)\,dz,\qquad p(z)=\mathcal N(0,I)
$$
적분이 불가능 → 근사 사후분포 $q_\phi(z\mid x)$(encoder)를 두고 **ELBO**를 최대화:

$$
\log p_\theta(x) \;\ge\;
\underbrace{\mathbb{E}_{q_\phi(z\mid x)}\big[\log p_\theta(x\mid z)\big]}_{\text{reconstruction}}
\;-\;
\underbrace{\mathrm{KL}\!\big(q_\phi(z\mid x)\,\Vert\,p(z)\big)}_{\text{regularization}}
$$

- **reconstruction**: decoder가 $z$에서 $x$를 복원.
- **KL 항**: 근사 사후분포가 prior에서 너무 멀어지지 않게 → latent 공간을 매끄럽게(샘플링 가능하게).
- **reparameterization trick**: $z = \mu_\phi(x) + \sigma_\phi(x)\odot\epsilon,\ \epsilon\sim\mathcal N(0,I)$ → 확률적 노드를 통과하는 gradient 계산 가능.
- 생성: $z\sim\mathcal N(0,I)$ 뽑아 decoder 한 번 → 빠름. 표본은 다소 흐림.

## 3. VQ-VAE

- encoder 출력을 연속이 아닌 **codebook**의 최근접 벡터 인덱스로 양자화(discrete latent).
- loss = 복원손실 + codebook loss + **commitment loss**(encoder 출력이 선택한 코드북 벡터에 머물도록 유도).
- gradient는 straight-through estimator로 encoder에 전달.
- prior $p(z)$는 별도로(예: PixelCNN/Transformer로) 이산 코드 시퀀스에 대해 학습 → **two-stage generation**: (1) prior로 코드 샘플 → (2) decoder로 이미지.

## 4. VQGAN

VQ-VAE의 재구성에 **GAN loss + perceptual loss**를 추가 → 같은 압축률에서 훨씬 선명한 복원. 이후 Transformer prior로 고해상도 이미지를 생성. 고품질은 autoencoder 하나가 아니라 **(VQ + adversarial/perceptual + 강력한 prior)의 조합**에서 나온다.

## 5. 계열 비교 (시험 정리)

| | 조건화 | 학습 목표 | 샘플링 |
|---|---|---|---|
| Autoregressive | 이전 토큰 | 정확한 NLL | 순차, 느림 |
| VAE | 연속 latent $z$ | ELBO(하한) | 1-step, 빠름, 흐림 |
| VQ-VAE | 이산 코드 + prior | 복원 + commitment | 2-stage |
| VQGAN | 이산 코드 + prior | 복원 + adversarial/perceptual | 2-stage, 선명 |

## 자주 틀리는 지점

- ELBO는 $\log p(x)$의 **하한**이다 — VAE는 likelihood를 정확히 계산하지 않는다.
- KL 항을 빼면 latent가 구조 없이 흩어져 prior 샘플링이 의미 없는 이미지를 만든다(posterior collapse는 반대로 KL이 0으로 붕괴하는 별도 문제).
- reparameterization 없이는 $q_\phi$의 파라미터로 backprop이 안 된다.
- VQ-VAE의 codebook은 파라미터지만, 이미지 품질의 큰 부분은 **prior 모델**과 (VQGAN이면) adversarial loss에서 온다.

## 복습 질문

- autoregressive 학습(teacher forcing)과 생성(순차)의 차이를 설명하라.
- ELBO를 유도하고 reconstruction·KL 두 항의 역할을 말하라.
- reparameterization trick이 필요한 이유와 식은?
- VQ-VAE의 commitment loss는 무엇을 유도하며, two-stage generation의 두 단계는?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **09. Autoregressive Models & VAE**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
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
- **09. Autoregressive Models & VAE**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [08. Generative Models](08-generative-models.md) · 다음: [10. Generative Adversarial Networks](10-generative-adversarial-networks.md)
