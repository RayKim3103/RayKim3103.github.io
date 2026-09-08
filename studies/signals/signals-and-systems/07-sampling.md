---
layout: page
title: "07. 샘플링"
permalink: /studies/signals/signals-and-systems/07-sampling/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Signals_and_Systems/lecture_notes/07%20%EC%83%98%ED%94%8C%EB%A7%81.md)

{% raw %}
## 핵심 요약

샘플링 정리는 일정 조건에서 CT 신호가 일정 간격의 샘플들만으로 완전히 표현될 수 있음을 말한다. 신호가 band-limited이고 샘플링 주파수가 최고 주파수의 두 배보다 크면 원 신호를 완벽히 복원할 수 있다. 조건을 만족하지 않으면 aliasing이 발생한다. 이 장은 impulse train sampling, reconstruction, interpolation, CT 신호의 DT 처리, DT decimation과 interpolation을 다룬다.

## 샘플링 정리

`X(jω)`가 `|ω| > ωM`에서 0인 band-limited 신호라 하자. 샘플링 주파수

```text
ωs = 2π / T
```

가 다음 조건을 만족하면

```text
ωs > 2ωM
```

샘플 `x(nT)`만으로 원래 CT 신호 `x(t)`를 유일하게 결정할 수 있다. `ωs = 2ωM`는 경계 조건 때문에 일반적으로 충분하다고 보지 않는다.

## Impulse train sampling

샘플링 함수는 impulse train으로 이상화할 수 있다.

```text
p(t) = Σ[n=-∞,∞] δ(t - nT)
xp(t) = x(t)p(t) = Σ x(nT)δ(t - nT)
```

시간 영역 곱셈은 주파수 영역 컨볼루션이므로 sampled signal의 spectrum은 원 스펙트럼의 주기적 복사본이 된다.

```text
Xp(jω) = (1/T)Σ[k=-∞,∞] X(j(ω - kωs))
```

복사본들이 겹치지 않으면 ideal LPF로 원 신호를 복원할 수 있다.

## 정확 복원

복원 필터는 cutoff가

```text
ωM < ωc < ωs - ωM
```

인 ideal LPF이고, gain은 `T`이다. 이 필터는 원 스펙트럼의 중앙 복사본만 남기고 나머지를 제거한다.

## Zero-order hold

실제 시스템에서 이상적인 impulse train은 만들기 어렵다. 그래서 DAC에서는 sample 값을 다음 sample까지 유지하는 zero-order hold를 사용한다. zero-order hold는 구현하기 쉽지만 magnitude droop와 phase delay를 만들므로 reconstruction filter로 보정한다.

## 보간

보간은 sample 값들을 통과하는 CT 신호를 만드는 과정이다.

- zero-order hold: 각 sample 값을 계단처럼 유지
- first-order hold: sample 사이를 직선으로 연결
- higher-order interpolation: 더 부드러운 다항식 또는 함수 사용
- band-limited interpolation: sampling theorem 조건에서 정확 복원

Band-limited interpolation 공식은 sinc 함수를 사용한다.

```text
xr(t) = Σ[n=-∞,∞] x(nT) sinc((t - nT)/T)
```

정확 복원은 이론적으로 가능하지만 ideal LPF와 무한 길이 sinc가 필요하므로 실제 구현에서는 근사한다.

## Aliasing

샘플링 주파수가 충분히 높지 않으면 주파수 복사본이 겹친다. 이때 서로 다른 CT 주파수가 같은 DT sequence로 보이는 aliasing이 발생한다.

정현파 예시에서

```text
x(t) = cos(ω0t + φ)
```

를 `T` 간격으로 샘플링하면

```text
x[n] = cos(ω0T n + φ)
```

이고, DT 주파수는 `2π` 주기이므로 여러 CT 주파수가 같은 샘플열을 만들 수 있다.

## Under-sampling의 효과

높은 주파수 성분이 낮은 주파수처럼 접혀 보인다. 회전하는 바퀴가 영화에서 천천히 돌거나 반대로 도는 것처럼 보이는 stroboscopic effect가 대표적이다.

aliasing을 막으려면 샘플링 전에 anti-aliasing low-pass filter로 입력 대역폭을 제한해야 한다.

## CT 신호의 DT 처리

실제 디지털 신호 처리 시스템은 다음 구조를 가진다.

```text
CT input -> C/D sampling -> DT system -> D/C reconstruction -> CT output
```

CT 주파수 `ω`와 DT 주파수 `Ω`는

```text
Ω = ωT
```

로 대응한다. 따라서 DT 시스템의 frequency response `Hd(ejΩ)`는 원래 CT 시스템에서 원하는 `Hc(jω)`를 `Ω = ωT`에 맞추어 설계해야 한다.

## 디지털 미분기

band-limited CT differentiator는

```text
Hc(jω) = jω, |ω| < ωc
```

로 표현된다. 샘플링 후 DT로 구현하려면 대응 주파수 `Ω = ωT`를 사용해

```text
Hd(ejΩ) = jΩ/T
```

형태로 설계한다.

## DT 신호의 샘플링과 decimation

DT impulse-train sampling은 `N`번째 sample만 남기는 과정이다.

```text
xp[n] = x[n]p[n]
p[n] = Σ[k=-∞,∞] δ[n-kN]
```

많은 0을 포함한 sequence를 그대로 저장하는 것은 비효율적이므로, 실제로는 downsampling을 사용한다.

```text
xb[n] = x[nN]
```

downsampling은 주파수 영역에서 spectrum compression과 aliasing을 만들 수 있다. 그래서 decimation 전에는 low-pass filtering으로 대역폭을 줄인다.

## Upsampling과 interpolation

upsampling은 sample 사이에 `N-1`개의 0을 삽입한 뒤 low-pass filtering을 적용하는 과정이다.

1. zero insertion
2. low-pass interpolation filtering

이는 sample rate conversion, 디지털 오디오, HDTV, 통신 시스템에서 핵심적으로 쓰인다.

## 연결 노트

- [이산시간 푸리에 변환](05-discrete-time-fourier-transform.md)
- [시간-주파수 특성](06-time-frequency-properties.md)
- [라플라스 변환과 z 변환](08-laplace-and-z-transforms.md)

{% endraw %}

---

이전: [06. 시간-주파수 특성](06-time-frequency-properties.md) · 다음: [08. 라플라스 변환과 z 변환](08-laplace-and-z-transforms.md)
