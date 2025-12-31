---
layout: page
title: Computer Abstraction
description: >
  This page tells about the Computer Abstraction
hide_description: false
sitemap: false
---

Table of contents

[OverView](#overview)  
[1. Computer Design Abstract](#1-computer-design-abstract)  
[2. Important Concepts in Computer Architecture](#2-important-concepts-in-computer-architecture)  
[3. Translating High-Level Language to Machine Code](#3-translating-high-level-language-to-machine-code)  
[4. Amdahl’s Law](#4-amdahl’s-law)  
[5. Power Wall and Multi Core](#5-power-wall-and-multi-core)
 
### OverView
* Computer Design Abstract
* Important Concepts in Computer Architecture
* Translating High-Level Language to Machine Code
* Amdahl’s Law
* Power Wall and Multi Core

### 1. Computer Design Abstract

![Computer Design Abstract](/assets/img/studies/ca/Computer Architecture/Computer Abstraction/1.png){: width="360" height="180"}

컴퓨터를 구성하는 요소는 여러 계층으로 이루어져 있다. Transistor 하나부터 Application까지 수억 개의 부품과 코드가 얽혀 있다.
이러한 계층 관계가 위 figure을 통해 나타난다.

1. Materials an Physics 및 Devices  
    주로 반도체 물성을 이용하여 Transistor와 같은 소자를 개발하는 단계이다. 2025년 기준 3nm 공정과 같은 기사를 흔히 볼 수 있는데, 이 단계의 Transistor Size(Length)를 의미한다.

1. Circuits  
    앞서 개발된 Transistor를 사용하여 특정 동작을 하는 회로를 구성하는 단계이다. 보통 학부 과정 중에서 흔히 설계하는 회로의 예시로 Filter를 들 수 있을 것 같다.

1. Logic Gates, RTL
    이 또한 회로 설계에 속하지만, 앞서 Circuits는 Sensor, ADC/DAC, Interface 등의 아날로그 domain에서의 설계를 말한다면, RTL단계는 standard cell로 미리 설계된 Logic Gates를 이용하여 회로 설계를 하는 것을 의미한다. 학부 과정 중에서 흔히 설계하는 회로의 예시로는 FPGA를 이용한 신호등 설계가 있을 것 같다.

1. Microarchitecture
    보통 CPU, GPU, NPU, QPU등의 설계에 있어서 내부의 구조 설계를 말하며, 예시로는 Pipe Lining, Branch Prediction, Memory Bus 등의 설계를 의미한다.

1. Instruction set architecture
    Computer는 그 자체로 돌아가는 것이 아니라, Software를 통해서 Hardware를 제어한다. 이 때, Software로 구성한 동작이 Hardware가 이해할 수 있는 언어로 변환되어야 한다. 이 언어를 Instruction이라고 하며, Instruction set architecture(ISA)를 통해 Hardware가 여러가지 동작을 수행한다. 보통, Coding을 하여 Computer에게 동작을 명령하는데, Coding에 사용한 High Level 언어가 Assembly, Machine Code로 변환된다. 이를 ISA라고 한다.

1. Operating Systems
    Computer를 사용하는 사용자는 일일히 직접 Computer에게 Coding을 통해서 명령을 하는 것이 아니라, Application이라는 형태로 보여지는 여러 프로그램을 클릭과 타이핑을 통해서 실행한다. 따라서, Computer에게는 자체적으로 이러한 여러 프로그램을 관리하는 System이 필요하며, 사용자의 입력을 받고 이를 Hardware가 이해할 수 있게 변환하는 System, 사용자에게 Hardware 그대로를 보여주는 것이 아닌 좀 더 친화적인 환경을 만들어 보여주는 Virtualization 등이 필요하다. 이러한 system을 우리는 Operating System이라고 부른다.


### 2. Important Concepts in Computer Architecture

![Important Concepts in Computer Architecture](/assets/img/studies/ca/Computer Architecture/Computer Abstraction/2.png){: width="720" height="360"}

2. Use abstraction to simplify a design

  새로운 컴퓨터 시스템 기술을 개발할 때, 하드웨어와 소프트웨어는 설계를 특징짓기 위해 추상화(abstractions)를 자주 사용한다. 낮은 수준의 세부 사항은 단순화되거나 숨겨진다.

2. Make the common cases faster

  작은 변화일지라도, 흔한 경우(common cases)를 더 빠르게 개선하는 것이 드문 경우(rare cases)를 크게 최적화하는 것보다 전체 시스템 성능을 더 효과적으로 향상시킨다. 대표적으로 이를 측정하는 metric으로 Amdahl's Law가 있다.

2. Performance scaling via parallelism

  컴퓨터 시스템은 다양한 수준에서 병렬성(parallelism)을 활용하도록 설계된다. 그러나 병렬성은 하드웨어와 소프트웨어 측면에서 적지 않은 오버헤드를 초래한다.

2. Performance scaling via pipelining

  일련의 작은 작업을 수행하는 것이 하나의 큰 작업을 처리하는 것보다 종종 더 빠르다. 작업을 더 작고 빠른 조각으로 나누는 방식으로 구현된다.

2. Performance scaling via prediction

  컴퓨터 프로그램은 특정 패턴을 반복하는 경향이 있다. 하드웨어는 곧 일어날 일을 예측(guess)할 수 있다. 그러나 예측이 틀리면 하드웨어는 엉망이 된 상태를 정리하고 다시 시작해야 한다. 예측 정확도가 높아야 잘못된 예측의 비용을 상쇄(amortize)할 수 있다.

2. Memory hierarchy

  프로그래머는 빠르고 용량이 크며 가격이 싼 메모리를 원한다. 그러나 용량이 큰 메모리는 느리고, 빠른 메모리는 용량이 작고 비싸다. 컴퓨터 시스템은 이러한 상충되는 요구를 해결하기 위해 메모리 계층 구조를 만든다. 대표적으로 폰 노이만 구조를 현대 컴퓨터 시스템에서 사용한다.

2. Reliability (dependability) via redundancy

  컴퓨팅 시스템은 반드시 신뢰할 수 있어야 한다. 실패는 임무-critical 시스템(예: 비행기, 위성, 자율 주행 차, 데이터센터 등)에서 치명적일 수 있다. 신뢰성을 높이는 간단하지만 비용이 드는 해결책은 시스템에 중복성(redundancy, 즉 백업)을 추가하는 것이다.

### 3. Translating High-Level Language to Machine Code

### 4. Amdahl’s Law

### 5. Power Wall and Multi Core
