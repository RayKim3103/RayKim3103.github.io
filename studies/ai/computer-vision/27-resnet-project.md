---
layout: page
title: "27. ResNet (project)"
permalink: /studies/ai/computer-vision/27-resnet-project/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 과제 보고서 `27` 보강
- 이론은 [04. Neural Networks & CNNs](04-neural-networks-and-cnns.md) 참고.

{% raw %}
## 개요

ResNet의 (1) layer별 **parameter 수** 계산, (2) **BatchNorm**의 역할과 train/eval 차이, (3) **cosine warmup** 스케줄러, (4) **Adam** optimizer의 원리를 실제 모델 설정으로 연결한다.

## 1. Parameter count

### Conv layer
$$
\#\text{params} = C_{\text{out}}\,(C_{\text{in}}\,K_h\,K_w)\ \ (+\,C_{\text{out}}\ \text{if bias})
$$
보통 conv 뒤에 BN이 오면 conv bias는 생략(BN의 $$\beta$$가 대신).

### BatchNorm layer
채널마다 학습 파라미터 2개: scale $$\gamma$$, shift $$\beta$$ → $$2C$$.
running mean/var($$2C$$)는 **버퍼**(학습 대상 아님)지만 eval에 직접 영향.

### FC layer
$$\#\text{params} = C_{\text{in}}\times C_{\text{out}} + C_{\text{out}}$$.

### ResNet 구조 요소
- **stem**: 큰 conv(예: 7×7, stride 2) + BN + ReLU + maxpool.
- **residual block**: (3×3 conv–BN–ReLU) ×2, 출력 $$y = x + F(x)$$.
  - 채널/해상도가 바뀌는 첫 block은 shortcut에 **1×1 conv(stride 2)** 를 넣어 차원 정합.
- **bottleneck block**(ResNet-50+): 1×1(축소) → 3×3 → 1×1(확장), 연산량 절감.
- **head**: global average pooling → FC(→ 클래스 수).

모델 summary를 볼 때 **parameter 수**와 **feature map 크기**를 따로 추적해야 memory·연산량을 옳게 해석한다.

## 2. BatchNorm

$$
\hat x = \frac{x - \mu_B}{\sqrt{\sigma_B^2 + \epsilon}},\qquad y = \gamma\hat x + \beta
$$

- **train**: 현재 미니배치 통계 $$\mu_B,\sigma_B^2$$ 사용 + running stat을 EMA로 갱신.
- **eval**: 학습 중 누적한 **running mean/var** 사용(배치에 무관하게 결정적).
- 효과: 더 큰 learning rate 허용, 초기화 민감도↓, 약한 정규화. `model.eval()`을 잊으면 추론이 배치 구성에 흔들린다.

## 3. Cosine warmup scheduler

$$
lr(t) =
\begin{cases}
lr_{\max}\cdot \dfrac{t}{T_{\text{warm}}}, & t \le T_{\text{warm}} \\[2mm]
lr_{\min} + \tfrac12 (lr_{\max}-lr_{\min})\big(1 + \cos(\pi\,\text{progress})\big), & t > T_{\text{warm}}
\end{cases}
$$
progress = $$(t - T_{\text{warm}})/(T_{\text{total}} - T_{\text{warm}})$$.

- **warmup**: 초반 불안정(큰 lr을 바로 쓰기 어려움, BN 통계 미성숙)을 선형 증가로 완화.
- **cosine decay**: 이후 부드럽게 감소 → step decay보다 안정적 수렴.

## 4. Adam

momentum(1차 모멘트)과 RMSProp(2차 모멘트)을 결합한 adaptive optimizer:
$$
m_t = \beta_1 m_{t-1} + (1-\beta_1) g_t,\qquad
v_t = \beta_2 v_{t-1} + (1-\beta_2) g_t^2
$$
$$
\hat m_t = \frac{m_t}{1-\beta_1^t},\quad
\hat v_t = \frac{v_t}{1-\beta_2^t},\qquad
\theta \leftarrow \theta - \eta\,\frac{\hat m_t}{\sqrt{\hat v_t} + \epsilon}
$$
- bias correction $$\hat m,\hat v$$: 초기 $$m_0=v_0=0$$ 편향 보정.
- 파라미터별로 유효 학습률이 다르다(gradient 크기에 반비례).
- Adam이 항상 SGD+momentum보다 **일반화**가 좋은 것은 아니다 — task/스케줄에 따라 비교(AdamW로 weight decay 분리 권장).

## 자주 틀리는 지점

- BN의 running statistics는 파라미터가 아니지만 eval 성능을 좌우한다.
- conv 뒤 BN이면 conv bias는 불필요(중복).
- warmup을 빼면 초반 큰 lr에서 발산/BN 불안정.
- Adam의 bias correction을 생략하면 초기 스텝이 과도하게 작아진다.

## 복습 질문

- $$C_{\text{in}}{=}64, C_{\text{out}}{=}128, K{=}3$$인 conv(BN 뒤따름)의 파라미터 수는?
- BatchNorm의 학습 파라미터와 버퍼를 구분하고, train/eval 동작 차이를 설명하라.
- cosine warmup의 두 구간 식과 각 구간의 목적은?
- Adam의 $$m_t, v_t$$가 각각 무엇을 추정하며 bias correction의 역할은?

{% endraw %}

---

이전: [25. Gaussian Pyramid (project)](25-gaussian-pyramid-project.md) · 다음: [99. 기말고사 리뷰](99-final-exam-review.md)
