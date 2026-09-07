---
layout: page
title: "07. Lab09 Quantization Interval Learning"
permalink: /studies/ai/deep-learning-experiment/07-lab09-quantization-interval-learning/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/07%20Lab09%20Quantization%20Interval%20Learning.md)

## 한눈에 보기
CNN의 weight와 activation을 낮은 bit-width로 양자화하여 메모리와 연산 비용을 줄이는 실습이다. 특히 Quantization Interval Learning(QIL)은 clipping/interval parameter를 학습 가능하게 두어 정보 손실을 줄인다.

## 핵심 개념
- network quantization
- QIL
- Straight-Through Estimator
- Custom Round
- QConv2d
- weight quantizer
- activation quantizer
- pretrained initialization

## 실습 흐름
- CIFAR-10 pretrained full-precision 모델을 불러와 기준 정확도를 확인한다.
- rounding의 미분 불가능성을 우회하기 위해 custom autograd function 또는 STE를 사용한다.
- weight quantizer와 activation quantizer를 구현하고 QConv2d에 삽입한다.
- full-precision weight를 초기값으로 삼아 quantized network를 fine-tuning한다.
- epoch마다 learned quantization parameter와 test accuracy를 기록한다.

## 구현과 이론의 연결
- 일반적인 hard rounding은 gradient가 0이거나 정의되지 않는다. STE는 forward에서는 quantized 값을 쓰고 backward에서는 identity처럼 gradient를 흘려 학습을 가능하게 한다.
- QIL은 단순 고정 clipping과 달리 interval 중심과 폭을 학습한다. 유효 범위 밖 값은 clip하고, 범위 안 값은 낮은 bit grid로 mapping한다.
- activation과 weight는 분포가 다르므로 quantizer parameter를 분리해야 한다. activation은 입력 batch statistics 변화에도 민감하다.

## 결과와 해석
- pretrained full-precision 모델의 test accuracy는 84.18%로 제시되었다.
- 초기 quantized accuracy는 39.00%로 크게 낮았지만 fine-tuning 후 최종 약 82.15%까지 상승했다. 중간 epoch 로그에서는 73.23%, 77.86%, 80.44% 등으로 회복되는 흐름이 확인된다.
- full-precision 대비 약간의 accuracy 손실이 남지만, 양자화를 통해 모델 압축과 연산 효율을 얻는 trade-off를 확인했다.

## 자주 헷갈리는 지점
- random initialization에서 바로 quantization을 학습하면 gradient noise와 정보 손실 때문에 수렴이 어렵다.
- quantization parameter에 gradient가 흐르는지 `requires_grad`와 optimizer parameter group을 확인해야 한다.
- bit-width가 낮을수록 saturation과 clipping에 민감하므로 activation histogram을 같이 보는 것이 좋다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab09 사전보고서 XNOR-Net과 양자화](20-prep-lab09-xnor-net.md)
- [Lab03 VGGNet과 ResNet 구현](01-lab03-vggnet-resnet.md)
- [Lab10 Neural Style Transfer](08-lab10-neural-style-transfer.md)


---

이전: [06. Lab08 Grad-CAM과 Guided Backpropagation](06-lab08-grad-cam-guided-backpropagation.md) · 다음: [08. Lab10 Neural Style Transfer](08-lab10-neural-style-transfer.md)
