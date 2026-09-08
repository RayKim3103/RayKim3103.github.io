---
layout: page
title: "09. 딥러닝 하드웨어와 Quantization"
permalink: /studies/arch/intelligent-system/09-deep-learning-hardware-and-quantization/
sitemap: false
---

- **원본**: [GitHub — Intelligent System](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Intelligent_System) · 강의 노트 `11` 정리·보강
- 팀 프로젝트(CNN accelerator)의 직접 배경. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

딥러닝 연산을 CPU/GPU/전용 하드웨어에서 실행하는 방식, convolution → GEMM 변환, accelerator **dataflow**(무엇을 PE 근처에 오래 둘 것인가), low-precision **quantization**.

## 1. CPU vs GPU

| 관점 | CPU | GPU |
|---|---|---|
| 목표 | **low latency** | **high throughput** |
| 강점 | 복잡한 control, single-thread | 대규모 병렬 (수많은 ALU, SIMT) |
| memory | cache hierarchy 중심 | shared memory / register 중심, latency를 많은 thread로 숨김 |
| control logic | OoO/speculative 비중 큼 | transistor를 computation에 더 많이 |
| DL 연산 | 가능하나 throughput 제한 | GEMM/conv에 적합 |

## 2. 딥러닝 layer = GEMM / MAC

### FC layer
$$
\mathbf{y} = W\mathbf{x} + \mathbf{b} \quad\xrightarrow{\text{batch } N}\quad Y = WX + B
$$

### Convolution → GEMM (im2col / Toeplitz)
1. input feature map의 local patch를 펼쳐 행렬 row/column
2. filter weights를 행렬로
3. GEMM
4. 결과를 output feature map shape로 reshape

- 장점: 기존 GEMM accelerator/library 재사용.
- 단점: input data가 반복 저장 → **memory footprint↑**.

### Multi-channel convolution 차원
- input: $C \times H \times W$, filter: $M \times C \times R \times S$, output: $M \times E \times F$
$$
O[m,e,f] = \sum_{c=0}^{C-1} \sum_{r=0}^{R-1} \sum_{s=0}^{S-1} I[c,\, e+r,\, f+s]\, W[m,c,r,s]
$$

### MAC
$$
psum \leftarrow psum + \text{activation} \times \text{weight}
$$
convolution·FC 모두 MAC의 반복. 하드웨어 핵심: MAC unit 수, **PE(processing element) array 구조**, activation/weight/psum data movement, local/global buffer 크기, **DRAM access 최소화**.

## 3. Memory가 진짜 병목

CNN은 병렬성이 높지만 **memory access가 병목** (AlexNet은 수억 MAC — worst case로 모두 DRAM이면 memory access energy가 지배).
$$
\text{목표: 비싼 DRAM 접근을 줄이고, 가까운 local memory에서 data reuse를 늘린다}
$$
memory hierarchy에서 **멀고 큰 memory일수록 접근 에너지가 크다** → activation·weight·partial sum을 PE 근처에서 재사용.

### GEMM Tiling
행렬을 작은 block으로 나눠 tile이 cache/shared memory에 들어가게 → 같은 tile data를 여러 MAC에 재사용 (naive는 temporal locality 낮음).

## 4. Dataflow (무엇을 stationary하게)

CNN의 data reuse 기회: 같은 weight가 여러 spatial location에 / 같은 activation이 여러 filter·window에 / partial sum이 누적.

| dataflow | PE 근처에 고정 | 목표 |
|---|---|---|
| **Output Stationary** | partial sum | psum read/write energy 최소화, local accumulation 최대화. weight·activation은 broadcast/multicast. output tile 크기·accumulator 용량 중요 |
| **Weight Stationary** | weight | weight read energy 최소화, filter reuse 최대화. activation broadcast, psum 공간적 accumulate. weight reuse·fetch 비용 큰 layer에 적합 |
| **Input Stationary** | activation/input feature map | activation read energy 최소화, input reuse 최대화 |
| **No Local Reuse** | (큰 global buffer 공유) | local reuse 적지만 DRAM access↓. activation multicast, weight single-cast |

## 5. Quantization

continuous/넓은 범위 값을 discrete set으로 제한. 전통 구현은 FP32/FP64지만 **inference는 8-bit / 16-bit로도 충분한 경우가 많다**.

**장점**: memory에 더 많은 data, cache/local buffer에 더 큰 model, 초당 더 많은 number 전송, SIMD 병렬성↑, 계산 속도↑, energy↓.
**단점**: 표현 범위 제한, quantization error.

### FP16 vs Fixed Point
| FP16 | bit | | Fixed ($p{+}q{+}1$ bit) | bit |
|---|---:|---|---|---:|
| sign | 1 | | sign | 1 |
| exponent | 5 | | integer part | $p$ |
| mantissa | 10 | | fractional part | $q$ |

- FP: 넓은 dynamic range, hardware cost 큼.
- Fixed: FPGA/ASIC에서 arbitrary bit-width 정하기 쉽고 hardware cost 낮음. (예: −1~1 값은 integer bit 없이 fractional 중심.)

### Scale Quantization
$$
\textbf{Symmetric: }\quad x_q = \operatorname{round}\big(s \cdot \operatorname{clip}(x, -\alpha, \alpha)\big)
$$
$$
\textbf{Asymmetric (scale + shift): }\quad x_q = \operatorname{round}\big(s \cdot \operatorname{clip}(x, \beta, \alpha) + z\big)
$$
$s$ = scaling factor, $\alpha,\beta$ = clipping threshold, $z$ = zero point. symmetric은 zero-point 처리가 단순(MAC hardware 단순). asymmetric은 분포가 0 중심이 아닐 때 bit range를 더 효율적으로.

### 무엇을 quantize?
- quantize: matrix multiply, FC, convolution, ReLU, pooling.
- quantize 안 하는 게 나음: softmax, tanh, sigmoid, GeLU (계산량 작고 nonlinear).

## 6. CNN Accelerator 설계 체크리스트

convolution loop order · input/weight/output buffer 구조 · channel과 tile dimension · MAC 병렬도 · psum accumulation 위치 · DRAM/BRAM access 횟수 · quantized data bitwidth · overflow와 scaling · **control/status register로 configurable parameter 전달**.

## 복습 질문

- convolution을 im2col로 GEMM화할 때의 장점과 단점은?
- "AI accelerator의 진짜 병목은 연산보다 data movement"의 의미와, memory hierarchy와 에너지의 관계는?
- output / weight / input stationary dataflow가 각각 무엇을 PE 근처에 고정하고 무엇을 최소화하는가?
- symmetric vs asymmetric scale quantization의 식 차이와, 각각 언제 유리한가?
{% endraw %}

---

이전: [08. Computer Vision과 CNN](08-computer-vision-and-cnn.md)
