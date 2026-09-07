---
layout: page
title: "09. Lab10-2 Neural Style Transfer 반복 실험"
permalink: /studies/ai/deep-learning-experiment/09-lab10-2-neural-style-transfer/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/09%20Lab10-2%20Neural%20Style%20Transfer%20%EB%B0%98%EB%B3%B5%20%EC%8B%A4%ED%97%98.md)

## 한눈에 보기
Lab10의 style transfer 구현을 바탕으로 initialization, alpha/beta 비율, content layer 위치, style image를 바꿔가며 결과 차이를 분석한 반복 실험 노트이다.

## 핵심 개념
- hyperparameter ablation
- initialization
- content/style balance
- content layer depth
- Picasso style
- mosaic style

## 실습 흐름
- content image 초기화, white-noise 초기화, style-image 초기화를 비교한다.
- mosaic style과 Picasso style 등 서로 다른 style target을 사용한다.
- alpha를 1e-1, 1e1처럼 조정해 content 보존 강도를 관찰한다.
- beta를 1e3~1e7 범위로 조정해 style 강도를 관찰한다.
- content layer를 conv2-2, conv3-4, conv4-3 등으로 바꿔 feature depth에 따른 결과를 비교한다.

## 구현과 이론의 연결
- content initialization은 빠르고 구조 보존이 강하다. white-noise initialization은 자유도가 높아 style texture가 강하지만 수렴이 느리다. style-image initialization은 색감과 texture가 빠르게 반영되지만 content layout 복원이 어려울 수 있다.
- alpha가 커지면 content feature reconstruction이 우세해 원본 구조가 유지된다. beta가 커지면 Gram matrix matching이 우세해 붓질과 색 분포가 강해진다.
- content layer가 shallow하면 pixel/edge 정합이 강하고, deep하면 semantic object layout을 중심으로 보존된다.

## 결과와 해석
- 여러 case에서 loss가 반복과 함께 감소했고, 예시 로그는 50~500 iteration 구간에서 꾸준히 줄어드는 형태를 보였다.
- style weight가 과도하게 클 때는 style texture가 화면 전체를 압도해 content 식별성이 낮아졌다.
- deep content layer를 사용할수록 작은 edge보다 큰 구조가 유지되어 style과 content의 융합이 자연스러워지는 경향을 보였다.

## 자주 헷갈리는 지점
- 실험 간 비교는 같은 content/style image, 같은 random seed, 같은 iteration 수를 유지해야 공정하다.
- style loss 값은 layer 수와 normalization에 따라 scale이 달라지므로 alpha/beta 절대값만으로 해석하면 위험하다.
- 결과 이미지는 정성 평가가 중요하지만, loss curve와 함께 보아야 수렴 실패와 취향 차이를 구분할 수 있다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab10 Neural Style Transfer](08-lab10-neural-style-transfer.md)
- [Lab10 사전보고서 Style Transfer](21-prep-lab10-style-transfer.md)
- [Lab12 CycleGAN](11-lab12-cyclegan.md)


---

이전: [08. Lab10 Neural Style Transfer](08-lab10-neural-style-transfer.md) · 다음: [10. Lab11 DCGAN](10-lab11-dcgan.md)
