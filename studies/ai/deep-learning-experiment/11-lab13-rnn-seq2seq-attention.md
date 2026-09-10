---
layout: page
title: "11. Lab13 — RNN · Seq2Seq · Attention"
permalink: /studies/ai/deep-learning-experiment/11-lab13-rnn-seq2seq-attention/
sitemap: false
---

- **원본**: [GitHub — Deep Learning Experiment](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Deep_Learning_Experiment) · 사전보고서(24) + 본보고서(12) 통합·보강
- 이론 배경은 표준 자료(Bahdanau attention 등) 수준으로 보강. **실험 설정·결과 수치는 원 보고서 그대로**입니다.

{% raw %}
## 개요

French → English 짧은 문장 번역에서 **GRU 기반 encoder–decoder**와 **attention decoder**를 구현한다. 고정 길이 context vector의 한계를 attention으로 보완하고, attention map으로 단어 정렬(alignment)을 해석.

## 1. 이론 배경

### RNN 계열
| | 게이트 | 특징 |
|---|---|---|
| vanilla RNN | 없음 | gradient vanishing/exploding → long-term dependency 약함 |
| **LSTM** | input / forget / output + cell state | 장기 기억 조절 |
| **GRU** | reset / update | LSTM보다 단순(게이트 수 적음), 장기 의존성 완화 |

### Seq2Seq + Attention
encoder가 source를 hidden state sequence로 만들고, decoder가 target을 생성.
- **bottleneck**: 하나의 고정 context vector에 문장 전체를 압축 → 문장이 길수록 심해짐.
- **Bahdanau attention**: decoder state와 encoder outputs 사이 alignment score → 매 decoding step마다 필요한 source 위치를 동적으로 참조.
  $$
  \alpha_{t,i} = \operatorname{softmax}\big(\text{score}(s_{t-1}, h_i)\big),\qquad
  c_t = \sum_i \alpha_{t,i}\, h_i
  $$
- **teacher forcing**: 학습 시 정답 단어를 다음 decoder input으로 사용 → 빠른 안정화. 하지만 train/inference mismatch(**exposure bias**) 발생. scheduled sampling 등이 완화 시도.
- **BLEU**: n-gram overlap 기반 metric → 번역 품질의 일부만 측정. attention map·실제 예문과 함께 봐야.

## 2. 실습 설계

- unicode normalization, lowercasing, punctuation 정리 → 문장 pair.
- `Lang` class로 word2index / index2word / word count vocabulary 구축.
- `EncoderRNN`: 단어 embedding → GRU hidden state sequence (forward/backward 결합).
- `AttnDecoderRNN`: 현재 input·hidden으로 attention weight 계산 → encoder outputs의 weighted context.
- teacher forcing으로 target 단어를 다음 input에 넣으며 **NLLLoss** 누적.
- 학습 **50,000 iteration**, 1,000 iteration마다 평균 loss 확인. attention map 시각화.

## 3. 결과

- loss: iter 1,000 `2.7917` → iter 21,000 `1.0715` 로 꾸준히 감소.
- attention map: 번역된 단어가 source의 **대응 위치에 높은 weight**를 두는지 확인 → 모델이 alignment를 학습했는지에 대한 질적 검증.

## 4. 자주 틀리는 지점

- **EOS token 누락** 시 decoder가 종료 시점을 못 배움.
- teacher forcing ratio를 과도하게 높이면 inference의 autoregressive 오류 누적에 취약.
- attention weight matrix의 **row/column이 source/target 중 어느 축**인지 명확히 표시해야 해석을 그르치지 않음.

## 복습 질문

- vanilla RNN의 long-term dependency 문제와, GRU/LSTM이 이를 완화하는 방식은?
- Seq2Seq의 bottleneck 문제와 attention이 이를 해결하는 원리($$\alpha_{t,i}$$, $$c_t$$)는?
- teacher forcing의 이점과 exposure bias는?
- attention map을 해석할 때 무엇을 확인하며, BLEU만으로 부족한 이유는?
{% endraw %}

---

이전: [10. Lab12 — Pix2Pix & CycleGAN](10-lab12-pix2pix-cyclegan.md) · 다음: [12. Project — CARE (Long-tailed Recognition)](12-project-care-long-tailed-recognition.md)
