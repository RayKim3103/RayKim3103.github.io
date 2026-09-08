---
layout: page
title: "08. Datapaths — 가산기 · 시프터 · 곱셈기"
permalink: /studies/circuits/cmos-ic/08-datapaths-adders-shifters-multipliers/
sitemap: false
---

- **원본**: [GitHub — CMOS Integrated Circuit](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_CMOS_Integrated_Circuit) · `08 Datapaths — 가산기 시프터 곱셈기` 보강

{% raw %}
## 개요

Datapath building block — **adder, comparator, shifter, multi-input adder, multiplier**. 특히 carry-propagate adder의 여러 구조가 만드는 **area / power / delay tradeoff**를 비교.

---

## 1. Boolean Logical Operations

Datapath는 arithmetic뿐 아니라 bitwise 연산 포함 — AND, OR, XOR, XNOR, inversion은 ALU 기본 연산이며 **equality comparator / zero detector**에도 사용.

---

## 2. Half Adder / Full Adder

```text
Half Adder:  S = A ⊕ B          C = A·B
Full Adder:  S = A ⊕ B ⊕ Cin    Cout = majority(A, B, Cin) = AB + BCin + ACin
```

Full adder = 3개 1-bit 입력 → sum + carry.

---

## 3. Generate / Propagate / Kill

bit별 carry 동작 정의:

| 상태 | 조건 | 의미 |
|---|---|---|
| **Generate** `G` | `A·B = 1` | `Cin`과 무관하게 `Cout = 1` |
| **Propagate** `P` | `A ⊕ B = 1` | `Cout = Cin` |
| **Kill** `K` | `A + B = 0` | `Cin`과 무관하게 `Cout = 0` |

`Cout = G + P·Cin`. **Group G/P**로 여러 bit 구간의 carry를 병렬 계산:
```text
G_(i:k) = G_(i:j) + P_(i:j) · G_(j-1:k)
P_(i:k) = P_(i:j) · P_(j-1:k)
```

---

## 4. Full Adder Circuit 구현

| 방식 | 특징 |
|---|---|
| **Static CMOS** | 안정적, noise margin 우수 (28T 미러 구조 등) |
| **Optimized static** | carry path를 빠르게(critical) 설계 |
| **CPL** (complementary pass transistor logic) | 빠름, 그러나 swing 저하·복잡도 주의 |
| **Dual-rail domino** | 매우 빠름, area·power 큼 |

Ripple adder에서 보통 **`Cin → Cout` carry path가 critical path**.

---

## 5. Carry-Propagate Adder (CPA) 구조

### Carry-Ripple Adder
```text
FA0 → FA1 → FA2 → … → FA(N-1)
```
- 장점: area 최소, 설계 단순
- 단점: carry가 모든 bit 통과 → **delay `O(N)`**

### Carry-Skip Adder
group 전체가 propagate(`P_group = P_i·P_{i+1}·…·P_j = 1`)이면 carry가 ripple chain을 **건너뜀**.
- 적절한 block 크기 → **delay ≈ `O(√N)`**

### Carry-Lookahead Adder (CLA)
group G/P를 병렬 계산해 carry를 빠르게:
```text
G_(i:0) = G_i + P_i·G_(i-1:0)
P_(i:0) = P_i·P_(i-1:0)
```
- carry-ripple보다 빠름, 단 G/P logic·wire가 복잡.

### Carry-Select Adder
각 block에서 `Cin=0` / `Cin=1` 두 경우의 sum을 **미리 계산**, 실제 carry 도착 시 **mux 선택**.
- 장점: carry 대기 시간 감소
- 단점: 두 경우 모두 계산 → **area·power ↑**

### Tree (Parallel-Prefix) Adder
recursive lookahead로 carry를 **`O(log N)` depth**에 계산. **Kogge-Stone**(빠름·wire 많음), **Brent-Kung**(wire 적음·depth 큼), Sklansky 등.
- tradeoff: 빠른 delay ↔ 많은 wire track, routing 복잡도, 큰 capacitance·power.

### Adder Architecture Tradeoff

| 구조 | Delay | Area/Power | 특징 |
|---|---|---|---|
| Ripple | `O(N)` (큼) | 작음 | 단순 |
| Skip | `O(√N)` (중간) | 중간 | group propagate |
| Lookahead | 작음 | 큼 | carry 병렬 계산 |
| Select | 작음 | 큼 | 두 carry 경우 미리 계산 |
| Tree | `O(log N)` (매우 작음) | 큼 | wiring 복잡 |

---

## 6. Comparator와 Detector

```text
equal = AND_i ( A_i XNOR B_i )
```
- **1's detector** = N-input AND
- **zero detector** = 모든 bit가 0인지 (N-input NOR)
- magnitude comparator: `A − B`의 sign/carry-out 활용

---

## 7. Shifters

| 종류 | 설명 |
|---|---|
| **Logical shift** | 빈 bit를 0으로 채움 |
| **Arithmetic shift** | signed number의 **부호 bit 유지** (right shift 시 sign 확장) |
| **Rotate** | 밀려난 bit를 반대쪽으로 |

- **Funnel Shifter**: 2N-bit input에서 N-bit field를 선택 → shift와 rotate를 일반화.
- **Barrel Shifter**: 여러 단계 mux로 **한 cycle에** 임의 shift/rotate. **Logarithmic barrel shifter** = shift amount의 각 bit(1, 2, 4, …)에 해당하는 `log₂N` 단계.

---

## 8. Multi-Input Adders와 Carry-Save Addition

Full adder = **3 입력 → 2 출력** (3:2 compressor). N개 FA를 병렬 배치 → 여러 operand를 **carry-save 형태**(sum vector + carry vector)로 축약.

```text
3 inputs → sum + carry     (carry는 sum보다 한 bit 높은 weight)
```

**Carry-Save Adder (CSA)**: 최종 carry propagation을 **마지막 한 번으로 미룸** → multi-operand addition을 빠르게 (partial product 합산의 핵심).

---

## 9. Multiplier

```text
X · Y = Σ_i ( x_i ? (Y << i) : 0 )
```

- **Array multiplier**: 규칙적 구조(layout 유리), 그러나 partial product 수·adder delay·power 큼.
- 개선: **Booth encoding**(partial product 수를 ~절반으로), **carry-save reduction tree**(Wallace / Dadda tree로 `O(log N)` 축약), 마지막에 CPA 한 번.

---

## 시험·복습 체크포인트

- Generate / propagate / kill의 정의와 `Cout = G + P·Cin` 관계, group G/P 재귀식은?
- Ripple / skip / lookahead / select / tree adder의 delay 차수와 area·power를 비교할 수 있는가?
- Carry-select가 area를 더 쓰는 이유, carry-skip이 `O(√N)`이 되는 직관은?
- Carry-save adder가 multi-operand addition(곱셈기)에 유리한 이유는?
- Logical / arithmetic shift / rotate의 차이, logarithmic barrel shifter의 단계 수는?
- Array multiplier 대비 Booth encoding + Wallace tree가 개선하는 지점은?
{% endraw %}

---

이전: [07. 고급 CMOS Gates](07-advanced-cmos-gates.md) · 다음: [09. 순차회로와 Timing](09-sequential-circuits-and-timing.md)
