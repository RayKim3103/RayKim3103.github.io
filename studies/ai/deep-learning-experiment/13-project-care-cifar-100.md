---
layout: page
title: "13. Project CARE 장기꼬리 CIFAR-100 구현"
permalink: /studies/ai/deep-learning-experiment/13-project-care-cifar-100/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/13%20Project%20CARE%20%EC%9E%A5%EA%B8%B0%EA%BC%AC%EB%A6%AC%20CIFAR-100%20%EA%B5%AC%ED%98%84.md)

{% raw %}
## 한눈에 보기
CIFAR-100 long-tailed split에서 head class bias를 줄이기 위해 CARE(Class-Aware Rebalancing Experts)를 구현한 프로젝트 노트이다. Stage 1에서 강한 표현을 학습하고, Stage 2에서 목적이 다른 expert classifier들을 결합해 tail 성능을 보완한다.

## 핵심 개념
- long-tailed recognition
- CIFAR-100
- ResNet18CIFAR
- cosine classifier
- class-balanced sampler
- Balanced Softmax
- LDAM
- class center
- expert aggregation
- TTA

## 실습 흐름
- CIFAR-100 train set에서 class당 50장을 validation으로 빼고, 남은 train image는 $n_c=450-4c$ 규칙으로 long-tail split을 만든다.
- CIFAR용 ResNet-18은 첫 convolution을 3x3 stride 1로 바꾸고 maxpool을 제거한다. classifier는 cosine classifier를 사용한다.
- Stage 1은 label smoothing, MixUp, CutMix, RandomErasing, cosine schedule을 사용해 regularized representation을 학습한다.
- Stage 2는 early visual layer를 freezing하고 layer3/layer4와 expert head를 fine-tuning한다. Expert 0은 cRT/class-balanced, Expert 1은 Balanced Softmax, Expert 2는 LDAM과 class-center alignment를 담당한다.
- validation macro accuracy로 tau, aggregation rule, teacher mix, horizontal flip TTA를 선택한 뒤 test 성능을 보고한다.

## 구현과 이론의 연결
- long-tailed 학습에서 standard CE는 head class prior를 강하게 반영해 tail class decision boundary가 불안정해진다. CARE는 representation adaptation과 classifier rebalancing을 분리해 이 문제를 완화한다.
- cosine classifier는 feature와 weight를 normalize하여 logit을 angular similarity로 해석하게 한다. class norm 차이로 인한 head bias를 줄이는 데 도움이 된다.
- class center는 Stage 1 backbone feature의 class-wise 평균으로 계산한다. tail-aware center initialization과 center regularization은 tail class classifier가 안정적인 기준점을 갖도록 돕는다.

## 결과와 해석
- Stage 1 best validation macro accuracy는 73.5%, Stage 1 test macro accuracy는 73.35%였다.
- cRT baseline은 test overall 73.49%, head 80.56%, medium 74.52%, tail 65.18%를 기록했다.
- 최종 CARE는 validation macro 77.02%로 proposed 후보 중 가장 좋았고, test overall 76.30%, head 79.85%, medium 76.64%, tail 72.30%, macro 76.30%를 기록했다. best tau는 1.25, aggregation은 `prior_g0.50`, teacher mix는 0.7, TTA는 horizontal flip이었다.

## 자주 헷갈리는 지점
- validation set은 balanced이므로 macro accuracy 기준 model selection에 적합하지만, test distribution 해석과 분리해야 한다.
- teacher mix는 head/medium 성능 저하를 막는 데 유용하지만 tail 보정 효과를 희석할 수 있어 validation으로 조절해야 한다.
- expert aggregation은 단순 평균, prior-aware weighting, temperature/tau calibration에 따라 결과가 크게 달라진다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Project 사전보고서 CARE](25-prep-project-care.md)
- [Lab13 Attention 기반 기계번역](12-lab13-attention.md)
- [Lab09 사전보고서 XNOR-Net과 양자화](20-prep-lab09-xnor-net.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **13. Project CARE 장기꼬리 CIFAR-100 구현**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- 모델 구조를 읽을 때는 layer 이름보다 tensor shape, receptive field, skip connection, normalization 위치를 먼저 추적한다.
- loss가 직접 최적화하는 것과 metric이 평가하는 것을 분리해야 결과를 제대로 해석할 수 있다.
- 재현 실험은 seed, data split, augmentation, learning rate schedule, checkpoint 기준이 모두 결과에 영향을 준다.

### 문제 풀이 또는 구현 루틴

- 입력/출력 shape 표를 먼저 만든 뒤 forward pass를 작은 batch로 검증한다.
- 학습이 불안정하면 loss scale, gradient norm, learning rate, normalization, label 형식을 순서대로 확인한다.
- 보고서에는 baseline, ablation, failure case, 재현 가능한 hyperparameter를 함께 남긴다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 논문 그림만 보고 구현하면 padding, stride, channel order 같은 세부에서 쉽게 틀린다.
- validation 성능이 좋아도 data leakage가 있으면 의미가 없다.
- loss가 여러 개인 실험은 각 항의 scale을 따로 확인해야 한다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 모델의 inductive bias는 어떤 데이터 특성에 맞춰져 있는가?
- 성능 향상이 architecture 때문인지 training recipe 때문인지 어떻게 분리할 수 있는가?
- 결과 이미지만 보지 않고 어떤 수치와 failure case를 함께 확인해야 하는가?
- **13. Project CARE 장기꼬리 CIFAR-100 구현**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [12. Lab13 Attention 기반 기계번역](12-lab13-attention.md) · 다음: [14. Lab03 사전보고서 CNN 구조](14-prep-lab03-cnn.md)
