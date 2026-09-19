---
layout: page
title: Special Topics in IC
description: >
  연세대학교 대학원 "집적회로특론(SYS6300: Special Topics in IC)" — 고속 직렬 링크(High-Speed Serial Link/SerDes) 회로 설계 강의 노트.
hide_description: false
sitemap: false
permalink: /grad_studies/high_speed_serial_link_circuits/
---

- **강의명**: SYS6300, Special Topics in IC (집적회로특론)
- **강의자**: Kwanseo Park (박관서), Yonsei University, Dept. of System Semiconductor Engineering
- **학기**: 2026년 봄학기
- **강의 주제**: 칩과 칩 사이에서 초고속으로 데이터를 주고받는 회로 — High-Speed Serial Link(SerDes, High-Speed I/O)의 설계를 처음부터 끝까지 다룬다. 채널(전송선로) → 송신기(TX) → 수신기(RX) → 이퀄라이저(EQ) → 클록/데이터 복원(CDR)까지, 하나의 고속 트랜시버를 구성하는 모든 블록을 회로 레벨에서 학습한다.

## 강의 진행 방식

전반부(1~8주)는 표준적인 이론 강의로 진행되며, 후반부(9주 이후)는 오픈북 시험, 학계·산업 초청 세미나, 그리고 학생들의 논문 리뷰/연구 발표로 구성된다. 강의 슬라이드 외에도 매 강의를 녹음한 뒤 정리한 노트가 함께 제공되었으며, 이 노트들은 슬라이드의 이론적 설명에 실무적 트레이드오프와 최신 연구 맥락을 더해준다. 아래 강의 노트는 이 두 자료를 종합해서 정리했다.

## 강의 노트

### Part 1. 고속 링크 개관과 채널

1. [00. 강의 개요](00-course-overview.md)
2. [01. 고속 직렬 링크 개관](01-high-speed-serial-link-overview.md)
3. [02. 채널과 전송선로](02-channels-and-transmission-lines.md)

### Part 2. 송신기와 수신기

4. [03. 고속 송신기 (Transmitter) 설계](03-high-speed-transmitters.md)
5. [04. 고속 수신기 (Receiver) 설계](04-high-speed-receivers.md)

### Part 3. 이퀄라이제이션

6. [05. 이퀄라이제이션 기초와 송신단 이퀄라이저(TX FFE)](05-equalization-basics-and-tx-equalization.md)
7. [06. 수신단 이퀄라이저(CTLE·DFE)와 적응](06-rx-equalization-and-adaptation.md)

### Part 4. 클록과 데이터 복원, 그리고 최신 연구

8. [07. 클록 데이터 복원 (Clock and Data Recovery)](07-clock-and-data-recovery.md)
9. [08. 최신 연구 동향: 세미나·논문 리뷰 종합](08-advanced-research-trends.md)

## 참고

- 강의 진행: 이론 강의(1~8주) + 오픈북 시험(9주) + 초청 세미나(10~11주) + 논문 리뷰·연구 발표(12주 이후)
- 평가: 출석 20% · 시험 40% · 발표(20분) 40%
- 핵심 참고 자료: D.-K. Jeong, *Topics in IC Design* (SNU); J. Kim, *Advanced Digital Integrated Circuits* (SNU); S. Palermo, *ECEN720: High-Speed Links Circuits and Systems* (Texas A&M); W. J. Dally and J. W. Poulton, *Digital Systems Engineering*, Cambridge University Press, 1998
