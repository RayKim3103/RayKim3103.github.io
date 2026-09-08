---
layout: page
title: "07. Lab09 — Network Quantization (QIL)"
permalink: /studies/ai/deep-learning-experiment/07-lab09-network-quantization/
sitemap: false
---

- **원본**: [GitHub — Deep Learning Experiment](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Deep_Learning_Experiment) · 사전보고서(20) + 본보고서(07) 통합·보강
- 이론 배경은 BWN/XNOR-Net/QIL 논문 수준으로 보강. **실험 설정·결과 수치는 원 보고서 그대로**입니다.

{% raw %}
## 개요

CNN의 weight·activation을 **낮은 bit-width**로 양자화해 메모리·연산 비용을 줄인다. 특히 **QIL(Quantization Interval Learning)**은 clipping/interval 파라미터를 **학습 가능**하게 두어 정보 손실을 줄인다. CIFAR-10 pretrained full-precision 모델을 fine-tuning.

## 1. 이론 배경

### Binary/low-bit 계보
- **BWN(Binary Weight Network)**: weight만 binary + scaling factor $\alpha$로 full-precision conv 근사.
  $$
  W \approx \alpha B,\qquad B\in\{-1,+1\}
  $$
- **XNOR-Net**: weight·activation **둘 다** binary → convolution을 **XNOR + bitcount**로 대체(하드웨어에 매우 유리). 단, 표현 함수 공간이 급감해 accuracy 손실 큼 → 첫 layer/classifier는 full precision 유지.
- **QIL / EWGS**: learnable interval 또는 gradient scaling으로 quantized model의 trainability 향상.

### Straight-Through Estimator (STE)
hard rounding/sign은 gradient가 0이거나 미정의. STE는 **forward에는 quantized 값, backward에는 identity**로 gradient를 흘려 학습을 가능하게 한다.

### QIL
고정 clipping과 달리 **interval의 중심과 폭을 학습**. 유효 범위 밖은 clip, 범위 안은 낮은 bit grid로 mapping → "어떤 값을 보존/포화할지"를 데이터에 맞춰 결정.
양자화 4단계: **clipping → scaling → rounding → de-scaling**.

## 2. 실습 설계

- CIFAR-10 pretrained **full-precision** 모델 로드 → 기준 정확도 확인.
- custom autograd function 또는 STE로 rounding 미분 불가 우회 (`Custom Round`).
- **weight quantizer**, **activation quantizer** 구현 → `QConv2d`에 삽입.
- full-precision weight를 **초기값**으로 quantized network fine-tuning.
- epoch마다 learned quantization parameter·test accuracy 기록.

## 3. 구현 핵심

- **random init에서 바로** quantization을 학습하면 gradient noise·정보 손실로 수렴이 어렵다 → pretrained init 필수.
- quantization parameter에 gradient가 흐르는지 `requires_grad`와 optimizer parameter group 확인.
- **activation과 weight는 분포가 다름** → quantizer parameter 분리. activation은 batch statistics 변화에도 민감.

## 4. 결과

| 단계 | test accuracy |
|---|---|
| Full-precision (pretrained) | **84.18 %** |
| Quantized 초기 | **39.00 %** |
| Fine-tuning 중간 | 73.23 % → 77.86 % → 80.44 % |
| Quantized 최종 | **≈ 82.15 %** |

- full-precision 대비 약간의 손실이 남지만, **모델 압축 + 연산 효율**을 얻는 trade-off를 확인.

## 5. 자주 틀리는 지점

- bit-width가 낮을수록 saturation·clipping에 민감 → **activation histogram**을 함께 봐야.
- quantization parameter가 실제로 학습되는지(gradient 경로) 확인.
- STE는 근사이므로 forward/backward 불일치에서 오는 오차를 감안.

## 복습 질문

- STE가 필요한 이유와, forward/backward에서 각각 무엇을 하는가?
- BWN과 XNOR-Net의 차이(무엇을 binary화하는가)와 각 trade-off는?
- QIL이 고정 clipping보다 나은 이유는?
- quantized 학습을 random init 대신 pretrained init에서 시작하는 이유는?
{% endraw %}

---

이전: [06. Lab08 — CAM & Grad-CAM](06-lab08-cam-grad-cam.md) · 다음: [08. Lab10 — Neural Style Transfer](08-lab10-neural-style-transfer.md)
