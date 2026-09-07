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
보통 conv 뒤에 BN이 오면 conv bias는 생략(BN의 $\beta$가 대신).

### BatchNorm layer
채널마다 학습 파라미터 2개: scale $\gamma$, shift $\beta$ → $2C$.
running mean/var($2C$)는 **버퍼**(학습 대상 아님)지만 eval에 직접 영향.

### FC layer
$\#\text{params} = C_{\text{in}}\times C_{\text{out}} + C_{\text{out}}$.

### ResNet 구조 요소
- **stem**: 큰 conv(예: 7×7, stride 2) + BN + ReLU + maxpool.
- **residual block**: (3×3 conv–BN–ReLU) ×2, 출력 $y = x + F(x)$.
  - 채널/해상도가 바뀌는 첫 block은 shortcut에 **1×1 conv(stride 2)** 를 넣어 차원 정합.
- **bottleneck block**(ResNet-50+): 1×1(축소) → 3×3 → 1×1(확장), 연산량 절감.
- **head**: global average pooling → FC(→ 클래스 수).

모델 summary를 볼 때 **parameter 수**와 **feature map 크기**를 따로 추적해야 memory·연산량을 옳게 해석한다.

## 2. BatchNorm

$$
\hat x = \frac{x - \mu_B}{\sqrt{\sigma_B^2 + \epsilon}},\qquad y = \gamma\hat x + \beta
$$

- **train**: 현재 미니배치 통계 $\mu_B,\sigma_B^2$ 사용 + running stat을 EMA로 갱신.
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
progress = $(t - T_{\text{warm}})/(T_{\text{total}} - T_{\text{warm}})$.

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
- bias correction $\hat m,\hat v$: 초기 $m_0=v_0=0$ 편향 보정.
- 파라미터별로 유효 학습률이 다르다(gradient 크기에 반비례).
- Adam이 항상 SGD+momentum보다 **일반화**가 좋은 것은 아니다 — task/스케줄에 따라 비교(AdamW로 weight decay 분리 권장).

## 자주 틀리는 지점

- BN의 running statistics는 파라미터가 아니지만 eval 성능을 좌우한다.
- conv 뒤 BN이면 conv bias는 불필요(중복).
- warmup을 빼면 초반 큰 lr에서 발산/BN 불안정.
- Adam의 bias correction을 생략하면 초기 스텝이 과도하게 작아진다.

## 복습 질문

- $C_{\text{in}}{=}64, C_{\text{out}}{=}128, K{=}3$인 conv(BN 뒤따름)의 파라미터 수는?
- BatchNorm의 학습 파라미터와 버퍼를 구분하고, train/eval 동작 차이를 설명하라.
- cosine warmup의 두 구간 식과 각 구간의 목적은?
- Adam의 $m_t, v_t$가 각각 무엇을 추정하며 bias correction의 역할은?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **27. ResNet (project)**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 신경망 주제에서는 activation shape, parameter count, gradient path, normalization 위치를 함께 추적한다.
- 깊은 모델의 성능은 architecture뿐 아니라 initialization, optimizer, learning rate schedule, augmentation에 민감하다.
- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- 이미지 처리 연산은 필터 모양뿐 아니라 경계 조건, sampling, aliasing, scale 변화까지 함께 봐야 한다.

### 문제 풀이 또는 구현 루틴

- 문제를 2D appearance, 3D geometry, learning 문제 중 어디에 가까운지 먼저 분류한다.
- 수식이 나오면 coordinate frame과 tensor shape을 함께 적어 projection과 feature map 흐름을 놓치지 않는다.
- 실험 보고서는 qualitative image와 quantitative metric을 나란히 두고 failure case를 설명한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 시각적으로 좋아 보이는 결과가 metric상 좋은 결과와 항상 일치하지 않는다.
- resize, padding, normalization 같은 preprocessing 차이가 모델 성능을 크게 바꿀 수 있다.
- calibration과 two-view geometry에서는 scale이 정해지지 않는 값이 많아 절대 크기로 해석하면 안 된다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 방법이 조명, viewpoint, scale 변화에 어떻게 반응하는가?
- loss와 metric이 실제 원하는 시각 품질 또는 인식 성능을 잘 대변하는가?
- 실패 사례를 데이터 부족, 모델 capacity, 최적화, annotation noise 중 무엇으로 설명할 수 있는가?
- **27. ResNet (project)**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [25. Gaussian Pyramid (project)](25-gaussian-pyramid-project.md) · 다음: [99. 기말고사 리뷰](99-final-exam-review.md)
