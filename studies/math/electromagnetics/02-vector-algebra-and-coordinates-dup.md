---
layout: page
title: "02. 벡터 대수와 직교 좌표계 중복본"
permalink: /studies/math/electromagnetics/02-vector-algebra-and-coordinates-dup/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electromagnetics/lecture_notes/02%20%EB%B2%A1%ED%84%B0%20%EB%8C%80%EC%88%98%EC%99%80%20%EC%A7%81%EA%B5%90%20%EC%A2%8C%ED%91%9C%EA%B3%84%20%EC%A4%91%EB%B3%B5%EB%B3%B8.md)

## 핵심 요약

이 자료는 [벡터 대수와 직교 좌표계](01-vector-algebra-and-coordinates.md)와 같은 2-1장 벡터 해석 슬라이드의 중복본이다. 내용은 벡터 대수, 직교 좌표계, 좌표 변환, 스칼라장과 벡터장 입문, 벡터 적분의 필요성을 다시 다룬다. 별도 파일로 존재하므로 독립 노트로 남기되, 학습 내용은 01번 노트와 동일한 주제군으로 정리한다.

## 중복본에서 확인할 내용

- 내적은 투영이며, 특정 방향 성분을 추출하는 연산이다.
- 외적은 면적, 회전, 토크, 로렌츠 힘 방향을 설명하는 데 쓰인다.
- 직교 좌표계는 문제 대칭에 따라 선택한다.
- 원통좌표와 구좌표에서는 단위벡터가 위치에 따라 변한다.
- 스칼라장과 벡터장을 도입한 뒤 선적분, 면적분, 체적적분으로 확장한다.

## 전자기학에서의 의미

벡터 대수는 이후 모든 장 법칙의 기초가 된다.

| 벡터 연산 | 전자기학 사용 예 |
| --- | --- |
| `E · dl` | 전위차, 유도기전력 |
| `D · dS` | 전기 플럭스, 가우스 법칙 |
| `B · dS` | 자기 플럭스, 패러데이 법칙 |
| `J · dS` | 전류, 연속 방정식 |
| `v × B` | 자기력 |
| `dl × aR` | 비오-사바르 법칙 |

## 좌표계 선택의 기준

- 평판 구조이면 Cartesian 좌표가 자연스럽다.
- 무한 직선 도선, 원통 도체, 솔레노이드, 동축선은 Cylindrical 좌표가 자연스럽다.
- 점전하, 구 도체, 구대칭 전하분포는 Spherical 좌표가 자연스럽다.

좌표계를 잘 고르면 장의 방향이 하나의 단위벡터로 고정되고, 적분식이 단순한 대수식으로 줄어든다. 반대로 좌표계를 잘못 고르면 같은 물리를 설명하더라도 불필요한 성분 계산이 늘어난다.

## 복습 체크

- `A · B`가 0이면 두 벡터는 서로 수직이다.
- `A × B`의 방향은 오른손 법칙으로 결정한다.
- 원통좌표에서 `aφ` 방향은 반지름 방향 `ar`을 90도 회전한 접선 방향이다.
- 구좌표에서 미소 체적은 `R^2 sinθ dR dθ dφ`이다.
- 플럭스 계산에서는 장의 법선 성분만 기여한다.

## 연결 노트

- [벡터 대수와 직교 좌표계](01-vector-algebra-and-coordinates.md)
- [벡터 미적분과 장 이론](03-vector-calculus-and-field-theory.md)


---

이전: [01. 벡터 대수와 직교 좌표계](01-vector-algebra-and-coordinates.md) · 다음: [03. 벡터 미적분과 장 이론](03-vector-calculus-and-field-theory.md)
