---
layout: page
title: "17. Lab06 사전보고서 FCN"
permalink: /studies/ai/deep-learning-experiment/17-prep-lab06-fcn/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/17%20Lab06%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20FCN.md)

{% raw %}
## 한눈에 보기
Fully Convolutional Network와 DilatedNet을 중심으로 semantic segmentation의 배경을 정리한 사전보고서이다.

## 핵심 개념
- FCN
- dense prediction
- semantic segmentation
- skip architecture
- dilated convolution
- context module

## 이론 정리
- patchwise/sliding-window 방식은 각 pixel 주변 patch를 반복 계산하므로 매우 비효율적이다.
- FCN은 fully connected layer를 convolution으로 바꿔 한 번의 forward pass로 class score map을 출력한다.
- FCN-32s, FCN-16s, FCN-8s는 skip connection의 사용 정도에 따라 spatial detail 복원력이 달라진다.
- Dilated convolution은 pooling 없이 receptive field를 키워 dense prediction에서 context 정보를 유지한다.
- class imbalance와 boundary ambiguity 때문에 pixel accuracy보다 mIoU가 중요한 평가 지표가 된다.

## 중요한 수식과 관점
- mIoU: 각 class에 대해 $TP/(TP+FP+FN)$을 계산한 뒤 평균한다.
- Dilated convolution은 kernel element 사이에 간격을 두어 effective receptive field를 확장한다.

## 실습과의 연결
Lab06에서는 VGG 기반 FCN8s를 구현하고, fc6/fc7 및 context module에 dilation을 사용한 MySegModel로 개선을 시도했다.

## 추가 해석 포인트
- segmentation에서는 local texture와 global context가 모두 필요하다. FCN의 skip connection은 이 두 정보를 서로 다른 해상도에서 결합하는 장치다.
- deconvolution으로 해상도를 복원하더라도 pooling에서 사라진 boundary detail을 완전히 되살리기는 어렵다.
- dilated convolution은 receptive field를 키우지만 gridding artifact가 생길 수 있어 dilation rate 조합이 중요하다.
- mIoU는 rare class의 성능 저하를 더 잘 드러낸다. background pixel이 압도적인 데이터에서는 pixel accuracy만 보면 모델이 과대평가될 수 있다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab06 Semantic Segmentation](04-lab06-semantic-segmentation.md)
- [Lab07 사전보고서 Focal Loss와 RetinaNet](18-prep-lab07-focal-loss-retinanet.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **17. Lab06 사전보고서 FCN**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
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
- **17. Lab06 사전보고서 FCN**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [16. Lab05 사전보고서 FSRCNN](16-prep-lab05-fsrcnn.md) · 다음: [18. Lab07 사전보고서 Focal Loss와 RetinaNet](18-prep-lab07-focal-loss-retinanet.md)
