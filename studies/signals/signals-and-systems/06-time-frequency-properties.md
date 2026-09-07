---
layout: page
title: "06. 시간-주파수 특성"
permalink: /studies/signals/signals-and-systems/06-time-frequency-properties/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Signals_and_Systems/lecture_notes/06%20%EC%8B%9C%EA%B0%84-%EC%A3%BC%ED%8C%8C%EC%88%98%20%ED%8A%B9%EC%84%B1.md)

{% raw %}
## 핵심 요약

이 장은 신호와 시스템을 시간 영역과 주파수 영역에서 동시에 바라보는 방법을 다룬다. Fourier transform의 magnitude와 phase, LTI 시스템의 gain과 phase shift, group delay, Bode plot, 이상적ㆍ비이상적 필터의 시간 영역 특성을 연결한다. 핵심 메시지는 주파수 선택성을 날카롭게 만들수록 시간 영역 응답이 길어지고 ringing이 생길 수 있다는 trade-off이다.

## Magnitude와 Phase

Fourier transform은 복소수 함수이므로 두 방식으로 표현할 수 있다.

```text
X(jω) = XR(jω) + jXI(jω)
X(jω) = |X(jω)|ej∠X(jω)
```

DTFT도 같은 방식으로 표현한다.

```text
X(ejΩ) = |X(ejΩ)|ej∠X(ejΩ)
```

magnitude는 주파수 성분의 크기를 나타내고, phase는 각 주파수 성분의 상대적 시간 위치를 결정한다. 음성은 약한 phase distortion에 상대적으로 둔감할 수 있지만, 영상에서는 edge 정보가 phase에 많이 들어 있어 phase가 매우 중요하다.

## LTI 시스템의 magnitude-phase 표현

LTI 시스템에서는

```text
Y(jω) = H(jω)X(jω)
```

이므로 magnitude와 phase는 다음처럼 분리된다.

```text
|Y(jω)| = |H(jω)||X(jω)|
∠Y(jω) = ∠H(jω) + ∠X(jω)
```

따라서 `|H|`는 gain, `∠H`는 phase shift이다.

## 선형 위상

CT LTI 시스템에서

```text
H(jω) = e-jωt0
```

이면 출력은

```text
y(t) = x(t - t0)
```

이다. 즉 크기는 바뀌지 않고 시간 지연만 생긴다. DT에서도 slope가 정수 지연에 해당하면 비슷하게 해석된다.

비선형 위상은 주파수 성분마다 다른 지연을 만들기 때문에 파형을 크게 왜곡할 수 있다.

## Group Delay

group delay는 phase의 음의 기울기이다.

```text
τg(ω) = -d∠H(jω)/dω
```

narrowband 입력에서는 중심 주파수 근처의 group delay가 실제 시간 지연처럼 작용한다. group delay가 주파수에 따라 달라지면 주파수 성분들이 서로 다른 시간에 도착해 파형이 퍼지거나 왜곡된다. 통신망 품질 평가에서 중요한 지표이다.

## 로그 크기와 Bode plot

크기 응답은 dB로 표현할 수 있다.

```text
20 log10 |H(jω)|
```

0 dB는 gain 1, 20 dB는 gain 10, 약 6 dB는 gain 2에 해당한다. CT 시스템에서는 주파수 범위가 넓어 로그 주파수축을 쓰는 Bode plot이 유용하다. DT 시스템은 주파수 구간이 `[-π, π]`로 제한되므로 보통 로그 주파수축을 덜 사용한다.

## 이상적 주파수 선택 필터

CT 이상적 저역통과 필터는

```text
H(jω) = 1, |ω| < ωc
H(jω) = 0, |ω| > ωc
```

이고 impulse response는 sinc 형태이다.

```text
h(t) = sin(ωc t) / (πt)
```

DT 이상적 저역통과 필터도 비슷하게

```text
h[n] = sin(Ωc n) / (πn)
```

형태를 가진다.

## 시간-주파수 trade-off

이상적 필터는 주파수 영역에서는 완벽하게 날카로운 경계를 갖지만, 시간 영역 impulse response가 무한히 길고 양의 시간과 음의 시간에 모두 존재한다. 결과적으로 noncausal이며 실제로 정확히 구현할 수 없다.

통과대역을 좁히거나 전이대역을 급하게 만들면 impulse response와 step response가 길어지고 ringing이 커질 수 있다. 반대로 시간 영역 응답을 짧고 안정적으로 만들면 주파수 선택성은 완만해진다.

## Step response와 ringing

이상적 LPF의 step response는 overshoot와 ringing을 보일 수 있다. impulse response의 main lobe 폭은 대략 cutoff frequency의 역수에 비례한다. cutoff가 낮아지면 시간 영역에서 변화가 느려지고 rise time이 길어진다.

## 비이상적 필터

실제 필터는 다음 요구 사이에서 절충한다.

- passband를 얼마나 평탄하게 유지할 것인가
- stopband를 얼마나 강하게 억제할 것인가
- transition band를 얼마나 좁게 만들 것인가
- phase 또는 group delay를 얼마나 선형에 가깝게 만들 것인가
- step response의 overshoot와 settling time을 얼마나 줄일 것인가

강의에서는 Butterworth와 elliptic filter를 비교한다. Butterworth는 transition band가 넓지만 응답이 부드럽고, elliptic은 cutoff가 날카롭지만 ringing과 settling time 문제가 더 두드러질 수 있다.

## 응용 예시

- 이미지 필터: edge detector, solarize, median filter
- 오디오 이퀄라이저: 저음, 중음, 고음 boost 또는 attenuation
- voice reduction filter: 특정 음성 대역을 줄이는 band-stop 계열 처리

## 연결 노트

- [연속시간 푸리에 변환](04-continuous-time-fourier-transform.md)
- [이산시간 푸리에 변환](05-discrete-time-fourier-transform.md)
- [샘플링](07-sampling.md)

{% endraw %}

---

이전: [05. 이산시간 푸리에 변환](05-discrete-time-fourier-transform.md) · 다음: [07. 샘플링](07-sampling.md)
