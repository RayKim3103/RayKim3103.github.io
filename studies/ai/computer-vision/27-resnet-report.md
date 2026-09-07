---
layout: page
title: "27. ResNet 보고서"
permalink: /studies/ai/computer-vision/27-resnet-report/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/27%20ResNet%20%EB%B3%B4%EA%B3%A0%EC%84%9C.md)

{% raw %}
## 한눈에 보기
ResNet 보고서는 ResNet 구조의 parameter 수, BatchNorm의 역할, cosine warmup scheduler, Adam optimizer의 원리를 정리한 과제 보고서이다.

## 핵심 개념
- ResNet parameter count
- BatchNorm
- cosine warmup scheduler
- Adam
- momentum
- RMSProp
- train eval mode

## 체계적 정리
- stem convolution, residual block, skip convolution, final FC layer의 parameter count를 layer별로 계산한다.
- BatchNorm은 per-channel scale gamma와 shift beta를 학습하며, batch mean/variance로 activation을 정규화한다.
- BatchNorm은 학습을 쉽게 만들고 더 큰 learning rate를 허용하며 초기화에 대한 민감도를 줄인다.
- train mode에서는 batch statistics를 사용하고, eval mode에서는 running statistics를 사용한다.
- cosine warmup scheduler는 초반 linear warm-up으로 learning rate를 키운 뒤 cosine decay로 부드럽게 줄인다.
- Adam은 momentum의 first moment와 RMSProp의 second moment 추정을 결합한 adaptive optimizer다.

## 중요한 수식과 관점
- Conv parameter: $C_{out}C_{in}K_hK_w$ plus optional bias.
- BatchNorm learnable parameter: channel마다 $\gamma,\beta$ 두 개.
- Cosine decay: $lr=lr_{min}+0.5(lr_{max}-lr_{min})(1+\cos(\pi progress))$.
- Adam은 bias-corrected $m_t$, $v_t$로 parameter update scale을 조절한다.

## 구현과 학습 포인트
이 보고서는 Neural Networks 2의 ResNet/BatchNorm 내용을 실제 모델 계산으로 연결한다. 모델 summary를 볼 때 parameter count와 feature map size를 따로 추적해야 memory와 계산량을 올바르게 해석할 수 있다.

## 자주 헷갈리는 지점
- BatchNorm의 running statistics는 parameter가 아니지만 eval 성능에 직접 영향을 준다.
- warm-up은 큰 learning rate를 바로 쓰기 어려운 초반 불안정을 줄인다.
- Adam이 항상 SGD보다 일반화가 좋은 것은 아니므로 task와 schedule에 맞춰 비교해야 한다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Neural Networks 2](05-neural-networks-2.md)
- [Neural Networks](04-neural-networks.md)
- [Classification Regularization](03-2-classification-regularization.md)

{% endraw %}

---

이전: [26. Gaussian Pyramid 보고서](26-gaussian-pyramid-report.md) · 다음: [99. 기말고사 리뷰](99-final-exam-review.md)
