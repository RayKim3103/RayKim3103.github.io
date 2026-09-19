---
layout: page
title: "04. PyTorch 내부 구조와 PyTorch 2.x 컴파일 스택"
permalink: /grad_studies/ai_platform_optimization/04-pytorch-internals-and-pytorch2-compilation/
sitemap: false
---

- **강의**: PyTorch (Part 1~3: Essentials / PyTorch 2.x / Eager to Graph Compilation)

{% raw %}
## 개요

PyTorch는 현재 딥러닝 프레임워크 생태계에서 사실상 표준이다. 이 장은 (1) PyTorch가 왜 TensorFlow 1.x를 제쳤는지(Imperative vs Symbolic), (2) PyTorch 내부가 어떻게 구성되어 있는지(Tensor/Autograd/Dispatcher/ATen/C10), (3) PyTorch 2.0에서 어떻게 "Eager mode의 편의성"과 "Graph mode의 속도"를 동시에 얻었는지(TorchDynamo/AOTAutograd/PrimTorch/TorchInductor)를 순서대로 다룬다.

## Symbolic vs Imperative 프레임워크

| 구분 | Symbolic (Define-and-Run) | Imperative (Define-by-Run) |
|---|---|---|
| 작업 흐름 | 그래프 정의 → 컴파일 → 실행 | 한 줄씩 즉시 실행 |
| 디버깅 | 어려움 (세션 기반, 에러가 불투명) | 쉬움 (표준 Python 디버거 사용 가능) |
| 최적화 | 정적 전역 최적화 용이 | 동적 실행이라 최적화가 어려움 |
| 예시 | TensorFlow 1.x, Caffe, Theano | **PyTorch**, Chainer, TensorFlow 2.x |

PyTorch가 TensorFlow 1.x를 제친 결정적 이유 두 가지는 (1) **Imperative 실행의 편의성**(Python 디버거로 바로 디버깅 가능)과 (2) **거의 모든 신규 연구 모델이 PyTorch로 먼저 공개**되는 생태계 효과다. 이후 TensorFlow도 Eager mode를 도입하며 PyTorch를 뒤따랐다.

## PyTorch의 세 가지 추상화 레벨

1. **Tensor**: GPU에서 동작 가능한 imperative n차원 배열. NumPy와 유사하지만 GPU 가속이 더해진 "drop-in replacement".
2. **Variable (Autograd)**: 계산 그래프의 노드. 값(data)과 그래디언트(grad)를 함께 저장한다. `autograd.Variable`이 Tensor를 감싸 모든 연산을 그대로 지원하면서 자동미분 기능을 추가한다.
3. **Module (`torch.nn`)**: 신경망 레이어/컨테이너. 학습 가능한 파라미터(weight)와 forward 로직을 함께 캡슐화한다.

```text
forward pass는 NumPy 코드처럼 보이고,
c.backward() 호출 한 번으로 전체 그래디언트가 계산된다.
```

### `torch.nn`으로 네트워크 만들기

- **Containers**: `nn.Sequential`로 레이어를 빠르게 쌓거나, `nn.Module`을 상속한 커스텀 클래스로 `forward()`를 직접 제어
- **Layers**: `nn.Linear`, `nn.Conv2d`, `nn.LSTM` 등 학습 가능 파라미터를 내장한 표준 레이어
- **Loss / Optimizer**: 표준 손실 함수(MSE, CrossEntropy)와, 계산된 그래디언트로 전체 파라미터를 갱신하는 Optimizer

## Autograd와 동적 계산 그래프

PyTorch는 **Define-by-Run** 방식이다 — 계산 그래프가 코드 실행과 동시에 동적으로 만들어진다. 이 덕분에 Python의 표준 제어 흐름(`if/else`, 반복문)이 그대로 그래프 구조에 반영될 수 있어, RNN처럼 구조가 입력에 따라 달라지는 동적 네트워크에 특히 유리하다. `.backward()` 호출 시 이 그래프를 거슬러 올라가며 모든 그래디언트를 자동 계산한다.

## Eager Mode vs Graph Mode

| 구분 | 설명 |
|---|---|
| **Eager Mode** | API 호출 → 연산자가 즉시 실행 |
| **Graph Mode** | API 호출 → 그래프가 점진적으로 구성됨. 그래프 변환·최적화(예: Op Fusion) 후 실행. Backward 그래프도 함께 생성 |

그래프 모드로 가는 접근 방식은 크게 두 갈래다.

- **TF1 스타일 (Define-and-run)**: 개발자가 그래프를 명시적으로 구성 — 그래프 모드 "전용"
- **PyTorch / TF2 / JAX 스타일 (Define-by-run)**: 개발자는 그냥 Eager 코드를 작성하고, **그래프는 실행을 추적(tracing)해서 사후에 추출**된다 — Eager와 Graph 실행을 모두 지원

## PyTorch가 복잡한 이유: Dispatcher

`torch.matmul` 하나만 해도 입력 텐서의 shape 조합(1D×1D, 2D×1D, 2D×2D, 3D+ 등)이 매우 다양하고, 실행 환경도 Python → C++ → CUDA라는 언어 경계를 넘나들며, CPU/GPU/기타 디바이스, 동적 그래프 생성(Autograd), tracing 여부 등 다양한 실행 시나리오가 얽혀 있다.

이를 처리하기 위해 PyTorch는 **Dispatcher 메커니즘**을 사용한다 — 연산 호출이 여러 계층(dispatch → redispatch)을 거쳐 최종적으로 알맞은 backend 구현으로 라우팅된다.

### 공유 라이브러리 계층

```text
libtorch_python.so
        ↓
    libtorch.so
    ↓         ↓
libtorch_cuda.so   libtorch_cpu.so
    ↓
libc10_cuda.so
    ↓
  libc10.so
```

### 핵심 라이브러리: ATen과 C10

| 라이브러리 | 역할 | 네임스페이스 |
|---|---|---|
| **ATen** | 텐서 연산·수학 정의, 사용자에게 노출되는 고수준 API, Python 연동 지점 | `at` |
| **C10** | 메타데이터/메모리 관리, 연산 dispatch, backend 연동 지점 (Caffe2와 ATen의 공용 기반) | `c10` |

소스 구조 상으로는 `aten/src/ATen`(core/native/cuda 등), `c10`(core/cuda 등)이 수기로 작성되고, `torch/csrc/autograd/generated`나 `build/aten/src/aten` 아래에는 `native_functions.yaml`/`derivatives.yaml`로부터 **자동 생성된 코드**(dispatch 등록, autograd kernel 등)가 위치한다.

## PyTorch 2.x: Eager의 편의성 + Graph의 속도

PyTorch 2.x는 기존 Eager mode 사용 경험을 100% 유지하면서, 내부적으로는 컴파일 스택을 완전히 새로 만들었다. 핵심 컴포넌트 네 가지는 다음과 같다.

```text
사용자 코드
   ↓ (TorchDynamo: 안전하게 그래프를 추출)
FX Graph
   ↓ (AOTAutograd: forward+backward를 하나의 Joint Graph로)
   ↓ (PrimTorch: 2000+ 연산자를 ~250개의 primitive 연산으로 분해)
   ↓ (TorchInductor: 하드웨어별 고성능 코드 생성)
Triton 커널 (GPU) / C++·OpenMP (CPU)
```

### 1) TorchDynamo — 신뢰할 수 있는 그래프 추출

- CPython Frame Evaluation API(PEP 523)를 후킹하는 **Python 레벨 JIT 컴파일러**
- **Bytecode 분석**을 통해 실행 전에 그래프를 캡처(FX Graph)
- **Guard**: 타입·shape 등 가정이 유지되는지 매 실행마다 체크
- **Fallback**: 지원되지 않는 코드는 안전하게 Eager mode로 되돌아감

기존 `jit.trace`(연산 결과가 `if` 조건에 영향을 주면 안전하지 않은 결과를 낼 수 있음)와 `jit.script`(Python 서브셋 전용 인터프리터)의 한계를, Dynamo는 **CPython 자체에 통합된 tracing**으로 해결한다.

### 2) AOTAutograd — Ahead-of-Time 역전파 그래프

전통적인 tracing은 forward pass만 캡처한다. 학습에는 backward 그래프가 필요한데, 이는 보통 런타임에 즉석으로 구성된다. AOTAutograd는 **autograd 엔진 자체를 추적**해 backward 그래프를 실행 전에(ahead-of-time) 뽑아낸다. 그 결과 forward+backward를 아우르는 **하나의 Joint Graph**를 얻게 되어, 옵티마이저가 학습 스텝 전체를 전역적으로 최적화할 수 있다.

### 3) PrimTorch — 연산자 단순화

PyTorch에는 2000개가 넘는 연산자가 있어, 새 하드웨어 벤더가 이를 전부 구현하는 것은 사실상 불가능에 가까웠다. PrimTorch는 복잡한 연산을 더 작고 명시적인 **primitive 연산(~250개)**으로 분해한다 — 벤더는 이 작은 Prim 집합만 최적화하면 되고, 나머지는 TorchInductor가 자동으로 낮춰(lowering)준다.

### 4) TorchInductor — 고성능 코드 생성

PyTorch 2.0의 기본 컴파일러 백엔드로, 캡처된 그래프를 고성능 머신 코드로 매핑한다.

- **GPU**: OpenAI **Triton** 커널을 생성 (CUDA를 직접 작성하는 것보다 훨씬 단순) → Ch.10에서 Triton 자체를 다룬다.
- **CPU**: C++/OpenMP 코드 생성
- 최적화: 루프 퓨전(loop fusion), 메모리 계획(memory planning), 오토튜닝(autotuning)

## 실무: torch.compile 내부 동작과 백엔드 선택 (강의 노트 기반)

### `torch.compile()` 한 줄의 의미

```text
PyTorch 1.x (Eager, 한 줄씩 실행) → 느림
PyTorch 2.0 torch.compile() → Tracing → FX Graph 생성 → Inductor 백엔드로 최적화된 코드 생성
```

- **Operator Fusion**: 연속된 작은 연산(Conv → Bias → Activation)을 하나의 커널로 결합해 메모리 읽기/쓰기 횟수를 줄임
- **Memory Planning**: 더 이상 필요 없는 중간 activation의 메모리를 해제·재사용
- **Tiling**: 큰 텐서를 작은 타일로 나눠 캐시 효율을 높임
- **Guard/Fallback의 한계**: 동적 shape(가변 시퀀스 길이 등)에서는 tracing 시점의 가정이 깨지기 쉬워 Guard/Fallback이 빈번히 발생할 수 있다 — LLM처럼 입력 길이가 매번 달라지는 워크로드에서 주의해야 하는 지점이다.

### 딥러닝 컴파일러 백엔드 비교

| 컴파일러 | 개발 주체 | 강점 | 단점/특징 |
|---|---|---|---|
| **TorchInductor** | PyTorch 팀 | PyTorch와 가장 잘 맞물림, Triton/CUDA 코드 자동 생성 | 아직 성숙 중인 영역 존재 |
| **TensorRT** | NVIDIA | NVIDIA GPU에서 최강 수준의 커널 퓨전 | NVIDIA 전용 |
| **XLA** | Google | TPU 최적화 최고 수준, JAX와 연계 | GPU에서는 TorchInductor에 밀리는 경우 많음 |
| **TVM** | 오픈소스 | CPU/GPU/NPU 등 다양한 하드웨어 지원 (Ch.5) | 성능이 TensorRT에 약간 못 미치는 경우 있음 |
| **Triton** | 오픈소스 | CUDA보다 훨씬 간단하게 GPU 커널 작성 가능 (Ch.10) | 오토튜닝이 필요하며 아직 완전히 성숙하지 않음 |

실무 결론: **PyTorch 2.0은 "Graph Mode + Inductor"가 기본**이며, vLLM/SGLang 같은 추론 엔진들은 내부적으로 TorchInductor·Triton 기반 fusion을 적극 활용한다. 새로운 하드웨어(NPU)를 PyTorch 생태계에 연동할 때도 **TorchInductor의 backend를 만드는 것**이 사실상 표준 경로가 되고 있다.

## 시험·복습 체크포인트

- Symbolic과 Imperative 프레임워크의 차이, 그리고 PyTorch가 TensorFlow 1.x를 이긴 이유는?
- Tensor / Variable / Module 세 추상화 레벨이 각각 담당하는 역할은?
- TorchDynamo의 Guard와 Fallback이 하는 일은 무엇이며, 왜 필요한가?
- AOTAutograd가 "Joint Graph"를 만드는 이유와 그 이점은?
- PrimTorch가 벤더 입장에서 왜 중요한가?
- TorchInductor, TensorRT, XLA, TVM, Triton을 실무에서 어떤 기준으로 선택하는가?
{% endraw %}

---

이전: [03. 역전파·자동미분과 양자화 기초](03-backpropagation-autodiff-and-quantization-basics.md) · 다음: [05. 딥러닝 컴파일러](05-deep-learning-compilers.md)
