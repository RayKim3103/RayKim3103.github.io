---
layout: page
title: "10. 반도체 캐리어 (Intrinsic / Extrinsic Semiconductors)"
permalink: /studies/circuits/electronic-materials/10-intrinsic-extrinsic-semiconductors/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes) · 교재: Kasap Ch.5
- 이전: [09. 페르미 통계·방출·포논](09-fermi-statistics-emission-phonons.md) · 다음: [11. 확산·흡수·접촉·PN접합](11-diffusion-optical-absorption-contacts-pn-junction.md)

{% raw %}
## 개요

```text
intrinsic: 열/광 여기로 e–h pair, n = p = n_i
extrinsic: donor(E_C 바로 아래) → n-type / acceptor(E_V 바로 위) → p-type
열평형: np = n_i²
온도 영역: freeze-out → extrinsic → intrinsic
recombination·minority carrier lifetime → photoresponse
```

---

## 1. Intrinsic Semiconductor

### Si 결정과 band
- Si는 네 sp³ hybrid로 이웃과 공유결합
- 0 K: VB 가득, CB 비어 있음
- 유한 온도 / photon 흡수 → 전자가 VB → CB → **electron–hole pair**

### Carrier generation
$$
h\nu \ge E_g \;\Rightarrow\; e^- + h^+ \text{ 생성}
$$
- 열진동도 공유결합을 깨 e–h pair 생성
- **정공**은 실제 양전하 입자가 아니라, 이웃 결합 전자가 빈 자리로 이동하며 양전하처럼 거동하는 유효 carrier

### intrinsic 농도
$$
n_i = \sqrt{N_C N_V}\, e^{-E_g/2kT}
$$
Si 상온 $$n_i \approx 1.0\times10^{10}\,\text{cm}^{-3}$$. 온도에 지수적으로 강하게 의존.

## 2. 열평형과 Mass Action Law

$$
\boxed{np = n_i^2} \quad (\text{intrinsic, n-type, p-type 모두})
$$
- intrinsic: $$n = p = n_i$$
- n-type: 전자 = majority, 정공 = minority
- p-type: 정공 = majority, 전자 = minority

## 3. Extrinsic Semiconductor

### n-type doping
As, P 같은 **Group V**: Si 자리 치환 → 네 전자는 결합, 다섯 번째가 약하게 묶임 → 작은 에너지로 CB로 → 자유전자.
- **donor level은 $$E_C$$ 바로 아래** ($$E_C - E_D \sim$$ 수십 meV)
- ionized donor = $$D^+$$
- $$n \approx N_D$$ ($$N_D \gg n_i$$), $$\;p \approx n_i^2/N_D$$

### p-type doping
B 같은 **Group III**: 원자가전자 하나 부족 → 결합 하나에 전자 비어 hole.
- **acceptor level은 $$E_V$$ 바로 위**
- ionized acceptor = $$A^-$$
- $$p \approx N_A$$, $$\;n \approx n_i^2/N_A$$

### 전압 인가와 band tilting
전압 → 전자의 electrostatic potential energy $$-eV(x)$$ 변화 → energy band 전체 기울어짐 → carrier drift.

## 4. 온도 의존성

### Carrier 농도 세 영역

| 온도 | 지배 요인 | 특징 |
|---|---|---|
| 낮음 — **freeze-out** | donor/acceptor ionization | dopant가 완전히 이온화 안 됨 |
| 중간 — **extrinsic (saturation)** | dopant 농도 | majority carrier ≈ dopant 농도 |
| 높음 — **intrinsic** | band gap 넘는 thermal generation | $$n_i >$$ dopant carrier |

$$T_s$$ = donor가 거의 다 이온화되는 saturation 온도, $$T_i$$ 이상에서 intrinsic 우세.

### Mobility 온도 의존성
- 낮은 온도: **ionized impurity scattering** 지배 ($$\mu \propto T^{3/2}$$)
- 높은 온도: **lattice(phonon) scattering** 지배 ($$\mu \propto T^{-3/2}$$)
- doping 농도 ↑ → impurity scattering ↑ → $$\mu$$ ↓

## 5. 전도도의 온도 의존성

$$
\sigma = q(n\mu_n + p\mu_p)
$$
온도 ↑ → carrier 농도 ↑ 이지만 mobility ↓ → 경쟁. **intrinsic 영역**에서는 carrier 농도 증가가 압도적 → $$\sigma$$ 급증.

## 6. Degenerate Semiconductor

매우 높은 doping ($$\gtrsim 10^{19}\,\text{cm}^{-3}$$): donor level들이 겹쳐 band처럼 되고 CB와 overlap.
- degenerate n-type: $$E_F$$가 **CB 안**으로
- degenerate p-type: $$E_F$$가 **VB 안**으로
→ 금속처럼 높은 carrier 농도·낮은 저항 (Ohmic contact, tunnel diode에 활용).

## 7. Recombination과 Trapping

### Direct recombination
전자·정공이 직접 재결합 → 에너지를 **photon** 또는 lattice vibration으로. GaAs 등 **direct band gap**(CB min과 VB max의 $$k$$ 같음) → momentum conservation 잘 맞음 → radiative recombination → LED, laser.
(Si는 **indirect** → phonon 관여 필요 → radiative 효율 낮음.)

### Trap-assisted (SRH) recombination
band gap 내부 defect level이 전자/정공을 포획 → 재결합 촉진. dangling bond, impurity, grain boundary가 trap center.

## 8. Low-Level Injection과 Minority Carrier

n-type에 약한 빛 → excess $$\Delta n_n$$, $$\Delta p_n$$ 생성. **low-level injection**: $$\Delta n_n \ll n_{n0}$$.
- majority 전자 농도는 거의 불변
- minority 정공 농도는 상대적으로 크게 변함
→ 광응답·recombination dynamics는 **minority carrier lifetime $$\tau_h$$**에 민감.

## 9. Photoresponse와 Photocurrent

$$
\Delta p_n(t): \; \text{조명 ON → } \tau_h \text{로 steady state까지 상승}, \; \text{OFF → } \tau_h \text{로 지수 감소}
$$
- photoconductor/photodiode 응답속도는 carrier lifetime + transport time으로 제한
- photocurrent = 광생성 carrier가 전기장에 수집되며 흐르는 전류

---

## 복습 질문

- intrinsic에서 e–h pair가 생기는 방식, $$n_i = \sqrt{N_C N_V}\,e^{-E_g/2kT}$$의 온도 의존성은?
- donor level($$E_C$$ 아래)과 acceptor level($$E_V$$ 위)의 band diagram 위치, $$np = n_i^2$$가 열평형에서 의미하는 것은?
- freeze-out / extrinsic / intrinsic 온도 영역의 구분 기준은?
- impurity scattering과 lattice scattering이 mobility의 온도 의존성에 주는 반대 경향은?
- direct vs trap-assisted recombination의 차이, low-level injection에서 minority carrier 변화가 중요한 이유는?
{% endraw %}

---

이전: [09. 페르미 통계·방출·포논](09-fermi-statistics-emission-phonons.md) · 다음: [11. 확산·흡수·접촉·PN접합](11-diffusion-optical-absorption-contacts-pn-junction.md)
