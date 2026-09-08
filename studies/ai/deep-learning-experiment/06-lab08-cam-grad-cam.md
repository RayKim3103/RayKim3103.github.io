---
layout: page
title: "06. Lab08 — CAM & Grad-CAM"
permalink: /studies/ai/deep-learning-experiment/06-lab08-cam-grad-cam/
sitemap: false
---

- **원본**: [GitHub — Deep Learning Experiment](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Deep_Learning_Experiment) · 사전보고서(19) + 본보고서(06) 통합·보강
- 이론 배경은 CAM / Grad-CAM 논문 수준으로 보강. **실험 설정·관찰은 원 보고서 그대로**입니다.

{% raw %}
## 개요

CNN의 예측 근거를 **시각화**한다. pretrained AlexNet·VGG16에 대해 **Grad-CAM**(마지막 conv feature의 gradient로 class-discriminative 영역), **Guided Backpropagation**(입력 gradient의 fine detail), 둘을 결합한 **Guided Grad-CAM**을 구현.

## 1. 이론 배경

### CAM (Class Activation Mapping)
마지막 conv feature map 뒤에 **GAP + linear classifier**가 있어야 함. class $c$의 map = class weight로 가중한 feature map 합.
→ 구조 제약이 강하고 재학습이 필요할 수 있다.

### Grad-CAM
target class score $y^c$의 gradient를 이용 → **GAP 구조가 없어도, architecture 변경·재학습 없이** 기존 CNN·detection·captioning·VQA에 적용.
$$
\alpha_k^c = \frac{1}{Z}\sum_i\sum_j \frac{\partial y^c}{\partial A_{ij}^k},
\qquad
L^c = \operatorname{ReLU}\!\Big(\sum_k \alpha_k^c A^k\Big)
$$
$\alpha_k^c$ = feature map $k$의 중요도(gradient의 spatial 평균). ReLU로 양의 기여만 남김.

### Guided Backpropagation
ReLU backward에서 **음수 gradient를 차단** → edge/detail 강조. 단독으로는 class-discriminative하지 않음.

### Guided Grad-CAM
Grad-CAM(coarse location) ⊙ Guided Backprop(fine detail) → 둘을 동시에.

## 2. 실습 설계

- AlexNet·VGG16 구조를 직접 정의하고 **ImageNet pretrained weight를 정확히 매핑**.
- 관심 class score에 backward → 마지막 conv의 forward activation·backward gradient를 **hook**으로 저장.
- gradient GAP → $\alpha_k^c$ → weighted sum + ReLU → heatmap → 원본에 overlay.
- Guided Backprop의 input gradient와 결합 → Guided Grad-CAM.

## 3. 구현 핵심

- pretrained key/value 매핑이 **해석 결과에 직접 영향** — weight를 정확히 load하지 않으면 heatmap 품질이 크게 나빠짐.
- hook을 classifier 뒤쪽 layer에 잘못 걸면 **공간 정보를 잃어** heatmap이 무의미해짐.
- 모델을 `eval()`로 두지 않으면 dropout/BN 때문에 재현성이 흔들림.

## 4. 결과 · 관찰

- classifier weight를 정확히 load하지 않으면 heatmap 품질 급락 → 해석은 **모델 내부 표현 품질에 의존**.
- **AlexNet보다 VGG16에서 더 안정적인 localization** — 더 깊고 규칙적인 conv hierarchy가 class-specific feature를 잘 형성.
- Guided Grad-CAM이 Guided Backprop 단독보다 class label과 더 잘 맞음.

## 5. 자주 틀리는 지점

- random classifier weight로 만든 Grad-CAM은 **그럴듯해 보여도 신뢰 금지**.
- heatmap은 causal proof가 아니라 **diagnostic clue** — 더 강한 검증은 강조 영역 제거/삽입 perturbation test.
- Guided Backprop 단독은 class-discriminative하지 않을 수 있음 → Grad-CAM과 결합해 보기.

## 복습 질문

- CAM과 Grad-CAM의 적용 조건 차이(GAP 구조 필요 여부)는?
- Grad-CAM에서 $\alpha_k^c$와 $L^c$ 식의 의미와 ReLU의 역할은?
- Guided Backpropagation이 하는 일과, 단독으로 부족한 이유는?
- pretrained weight 매핑이 Grad-CAM 품질에 직접 영향을 주는 이유는?
{% endraw %}

---

이전: [05. Lab07 — RetinaNet & Focal Loss](05-lab07-retinanet-focal-loss.md) · 다음: [07. Lab09 — Network Quantization](07-lab09-network-quantization.md)
