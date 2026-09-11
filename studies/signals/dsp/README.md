---
layout: page
title: Digital Signal Processing
description: >
  Digital Signal Processing 강의 노트.
hide_description: false
sitemap: false
permalink: /studies/signals/dsp/
---

원본: [GitHub — Digital Signal Processing](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Digital_Signal_Processing)

> 01~13번 노트 본문에 원본 추출 과정에서 남은 중복 frontmatter/제목/네비게이션 블록(BOM 포함)이 그대로 끼어 있어 페이지에 깨진 텍스트로 노출되던 문제를 정리했습니다. 그 위에 각 장의 formula를 실제 숫자로 끝까지 계산하는 worked example(convolution·Toeplitz 행렬·부분분수 역변환·minimum-phase 분해·Yule-Walker·Wiener filter 비교·bilinear prewarping·4-point DFT·separable filter 계산량 등)을 추가했습니다. 여러 worked example은 같은 수열 $$x=\{1,2,3,4\}$$을 재사용해 convolution → Toeplitz 행렬 → DFT가 같은 결과를 낸다는 것을 장을 넘나들며 확인할 수 있게 했습니다.

## 강의 노트

1. [00. 강의계획과 DSP 전체 개요](00-course-plan-and-overview.md)
2. [01. 연구실 소개와 DSP 응용 맥락](01-lab-intro-and-dsp-applications.md)
3. [02. DSP Introduction](02-dsp-introduction.md) — 양자화 계단 간격 worked example
4. [03. Review of Signals and Systems](03-review-of-signals-and-systems.md) — convolution 전체 계산 worked example
5. [04. Sampling Rate Change](04-sampling-rate-change.md) — 8kHz→12kHz 비정수 변환 worked example
6. [05. LTI System to Linear Algebra](05-lti-system-to-linear-algebra.md) — Toeplitz 행렬로 03장과 동일 결과 검증
7. [06. Linear Algebra Inverse](06-linear-algebra-inverse.md) — 최소제곱 system identification worked example
8. [07. Z-Transform Introduction](07-z-transform-introduction.md) — 부분분수 역 z-transform worked example
9. [08. Z-Transform Analysis of LTI Systems](08-z-transform-analysis-of-lti-systems.md) — minimum-phase/all-pass 분해 숫자 검증
10. [09. Structures and Parametric Modeling](09-structures-and-parametric-modeling.md) — Yule-Walker AR(2) worked example
11. [10. Wiener Optimal Filter](10-wiener-optimal-filter.md) — 단순 inverse vs Wiener filter 숫자 비교
12. [11. Digital Filter Design](11-digital-filter-design.md) — bilinear transform prewarping worked example
13. [12. DFT and FFT](12-dft-and-fft.md) — 4-point DFT worked example
14. [13. 2D DSP Overview](13-2d-dsp-overview.md) — separable filter 계산량 절감 worked example
