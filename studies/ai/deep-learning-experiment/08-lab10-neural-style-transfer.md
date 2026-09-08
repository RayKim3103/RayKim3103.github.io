---
layout: page
title: "08. Lab10 — Neural Style Transfer"
permalink: /studies/ai/deep-learning-experiment/08-lab10-neural-style-transfer/
sitemap: false
---

- **원본**: [GitHub — Deep Learning Experiment](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Deep_Learning_Experiment) · 사전보고서(21) + 본보고서(08) + 반복 실험(09) 통합·보강
- 이론 배경은 Gatys / Johnson 논문 수준으로 보강. **실험 설정·관찰은 원 보고서 그대로**입니다.

{% raw %}
## 개요

pixel loss 대신 **pretrained VGG feature 공간**에서 content와 style을 정의한다.
- **Gatys (optimization 기반)**: 출력 이미지 자체를 변수로 두고 content + style loss를 최소화. 품질↑, 이미지마다 반복 최적화 필요.
- **Johnson (feed-forward)**: transform network를 미리 학습 → test time에 **1회 forward**로 stylize.

## 1. 이론 배경

$$
L_{\text{total}} = \alpha\,L_{\text{content}} + \beta\,L_{\text{style}} + \gamma\,L_{\text{tv}}
$$

- **content loss**: 특정 VGG layer feature의 L2 차이 → semantic layout.
- **style loss**: 여러 layer의 **Gram matrix** 차이 합.
  $$
  G_{ij} = \sum_k F_{ik} F_{jk}
  $$
  Gram matrix는 spatial 위치를 지우고 **채널 상관관계(texture 통계)**만 남긴다 → texture엔 강하고 구조 정보엔 약함.
- **total variation loss**: 인접 픽셀 차이를 줄여 output 고주파 잡음 완화.

> content·style은 물리량이 아니라 **pretrained recognition network가 학습한 representation**에서 정의된 개념.

### ImageTransformNet (Johnson)
9×9 initial/final conv, stride-2 downsampling ×2, **residual block 5개**, upsampling conv.

## 2. 실습 설계

- content·style image를 같은 크기·정규화로 로드. **VGG19**를 고정된 loss network로 사용, content layer·style layer 지정.
- optimization 기반: 출력 이미지를 변수로, LBFGS/Adam으로 content+style loss 최소화.
- feed-forward: `ImageTransformNet` 학습 → 1회 forward로 stylize.

### 반복 실험 (Lab10-2) — ablation
- **initialization**: content init(빠름·구조 보존 강함) / white-noise init(자유도↑·texture 강함·수렴 느림) / style-image init(색·texture 빠르게 반영·layout 복원 어려움).
- **α/β**: α↑ → content feature reconstruction 우세(구조 유지). β↑ → Gram matrix matching 우세(붓질·색 강해짐, 구조 왜곡·색 번짐 위험). α는 1e-1~1e1, β는 1e3~1e7 범위로 조정.
- **content layer 깊이**: conv2-2(shallow, edge/pixel 정합) → conv4-3(deep, semantic object layout 보존).

## 3. 결과 · 관찰

- optimization 기반: 품질 좋으나 이미지 한 장마다 반복 최적화.
- feed-forward: 학습 후 inference 빠름 → **실시간 style transfer**에 적합.
- 여러 case에서 loss가 반복과 함께 감소(예: 50~500 iteration에서 꾸준히 감소). style weight가 과도하면 texture가 화면을 압도해 content 식별성↓.
- **deep content layer**를 쓸수록 작은 edge보다 큰 구조가 유지 → style/content 융합이 자연스러워짐.

## 4. 자주 틀리는 지점

- VGG input normalization을 content/style/generated에 **동일하게** 적용해야 loss scale이 의미 있다.
- Gram matrix는 spatial size에 따라 scale이 달라짐 → **normalization 포함**해야 layer별 loss 균형.
- style/content layer 선택이 결과를 크게 좌우(shallow=texture/edge, deep=semantic layout).
- ablation 비교는 같은 image·seed·iteration 수를 유지해야 공정. loss curve와 정성 평가를 함께 본다.

## 복습 질문

- content loss와 style loss가 각각 무엇의 차이로 정의되며, Gram matrix가 잡는 것은?
- α, β를 각각 키우면 결과가 어떻게 변하는가?
- content layer를 shallow → deep으로 바꾸면 보존되는 것이 어떻게 달라지는가?
- optimization 기반과 feed-forward 방식의 속도/품질 trade-off는?
{% endraw %}

---

이전: [07. Lab09 — Network Quantization](07-lab09-network-quantization.md) · 다음: [09. Lab11 — DCGAN](09-lab11-dcgan.md)
