---
layout: page
title: "12. Lab13 Attention 기반 기계번역"
permalink: /studies/ai/deep-learning-experiment/12-lab13-attention/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/12%20Lab13%20Attention%20%EA%B8%B0%EB%B0%98%20%EA%B8%B0%EA%B3%84%EB%B2%88%EC%97%AD.md)

{% raw %}
## 한눈에 보기
French-English 짧은 문장 번역 문제에서 GRU 기반 encoder-decoder와 attention decoder를 구현하는 실습이다. 고정 길이 context vector의 한계를 attention으로 보완하고, attention map으로 단어 정렬을 해석한다.

## 핵심 개념
- RNN
- GRU
- Seq2Seq
- encoder-decoder
- teacher forcing
- attention
- alignment
- NLLLoss

## 실습 흐름
- unicode normalization, lowercasing, punctuation 정리를 거쳐 문장 pair를 만든다.
- Lang class로 word2index, index2word, word count vocabulary를 구축한다.
- EncoderRNN은 입력 단어를 embedding한 뒤 GRU hidden state sequence를 만든다. 양방향 처리를 위해 forward/backward output을 결합한다.
- AttnDecoderRNN은 현재 decoder input과 hidden state로 attention weight를 계산하고 encoder outputs의 weighted context를 만든다.
- teacher forcing을 사용해 target 단어를 다음 decoder input으로 넣으며 NLLLoss를 누적해 학습한다.

## 구현과 이론의 연결
- GRU는 reset/update gate를 통해 vanilla RNN의 long-term dependency 문제를 완화한다. LSTM보다 gate 수가 적어 계산이 간단하다.
- attention은 decoder가 매 time step마다 source sentence의 어느 위치를 볼지 결정한다. 이 덕분에 긴 문장에서도 하나의 고정 context vector에 모든 정보를 압축하지 않아도 된다.
- 학습은 50,000 iteration 설정이며, 출력 로그에서 1,000 iteration마다 평균 loss를 확인한다.

## 결과와 해석
- loss는 iter 1,000의 2.7917에서 iter 21,000의 1.0715까지 꾸준히 감소했다.
- attention map은 번역된 단어가 source sentence의 대응 위치에 높은 weight를 두는지 확인하는 해석 도구로 사용되었다.
- teacher forcing은 학습을 빠르게 안정화하지만, inference에서는 모델 자신의 예측을 다음 입력으로 사용하므로 exposure bias가 생길 수 있다.

## 자주 헷갈리는 지점
- EOS token을 누락하면 decoder가 종료 시점을 배우지 못한다.
- teacher forcing ratio를 과도하게 높이면 inference의 autoregressive 오류 누적에 취약해질 수 있다.
- attention weight matrix의 row/column이 source/target 중 어느 축인지 명확히 표시해야 해석을 잘못하지 않는다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab13 사전보고서 RNN과 Seq2Seq](24-prep-lab13-rnn-seq2seq.md)
- [Lab12 CycleGAN](11-lab12-cyclegan.md)
- [Project CARE 장기꼬리 CIFAR-100 구현](13-project-care-cifar-100.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **12. Lab13 Attention 기반 기계번역**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 신경망 주제에서는 activation shape, parameter count, gradient path, normalization 위치를 함께 추적한다.
- 깊은 모델의 성능은 architecture뿐 아니라 initialization, optimizer, learning rate schedule, augmentation에 민감하다.
- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- 모델 구조를 읽을 때는 layer 이름보다 tensor shape, receptive field, skip connection, normalization 위치를 먼저 추적한다.

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
- **12. Lab13 Attention 기반 기계번역**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [11. Lab12 CycleGAN](11-lab12-cyclegan.md) · 다음: [13. Project CARE 장기꼬리 CIFAR-100 구현](13-project-care-cifar-100.md)
