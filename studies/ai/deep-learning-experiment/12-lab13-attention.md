---
layout: page
title: "12. Lab13 Attention 기반 기계번역"
permalink: /studies/ai/deep-learning-experiment/12-lab13-attention/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/12%20Lab13%20Attention%20%EA%B8%B0%EB%B0%98%20%EA%B8%B0%EA%B3%84%EB%B2%88%EC%97%AD.md)

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


---

이전: [11. Lab12 CycleGAN](11-lab12-cyclegan.md) · 다음: [13. Project CARE 장기꼬리 CIFAR-100 구현](13-project-care-cifar-100.md)
