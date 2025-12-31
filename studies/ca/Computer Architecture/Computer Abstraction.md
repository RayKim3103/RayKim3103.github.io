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
[1. Computer Design Abstract](#1-Computer-Design-Abstract)  
[2. Important Concepts in Computer Architecture](#2-Important-Concepts-in-Computer-Architecture)  
[3. Translating High-Level Language to Machine Code](#3-Translating-High-Level-Language-to-Machine-Code)  
[4. Amdahl’s Law](#4-Amdahl’s-Law)
[5. Power Wall and Multi Core](#5-Power-Wall-and-Multi-Core)
 
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

1. Materials an Physics 및 Devices: 주로 반도체 물성을 이용하여 Transistor와 같은 소자를 개발하는 단계이다. 2025년 기준 3nm 공정과 같은 기사를 흔히 볼 수 있는데, 이 단계의 Transistor Size(Length)를 의미한다.

2. Circuits: 앞서 개발된 Transistor를 사용하여 특정 동작을 하는 회로를 구성하는 단계이다. 보통 학부 과정 중에서 흔히 설계하는 회로의 예시로 Filter를 들 수 있을 것 같다.

3. Logic Gates, RTL: 이 또한 회로 설계에 속하지만, 앞서 Circuits는 Sensor, ADC/DAC, Interface 등의 아날로그 domain에서의 설계를 말한다면, RTL단계는 standard cell로 미리 설계된 Logic Gates를 이용하여 회로 설계를 하는 것을 의미한다. 학부 과정 중에서 흔히 설계하는 회로의 예시로는 FPGA를 이용한 신호등 설계가 있을 것 같다.

4. Microarchitecture: 보통 CPU, GPU, NPU, QPU등의 설계에 있어서 내부의 구조 설계를 말하며, 예시로는 Pipe Lining, Branch Prediction, Memory Bus 등의 설계를 의미한다.

5. Instruction set architecture: Computer는 그 자체로 돌아가는 것이 아니라, Software를 통해서 Hardware를 제어한다. 이 때, Software로 구성한 동작이 Hardware가 이해할 수 있는 언어로 변환되어야 한다. 이 언어를 Instruction이라고 하며, Instruction set architecture(ISA)를 통해 Hardware가 여러가지 동작을 수행한다. 보통, Coding을 하여 Computer에게 동작을 명령하는데, Coding에 사용한 High Level 언어가 Assembly, Machine Code로 변환된다. 이를 ISA라고 한다.

6. Operating Systems: 


### 2. Important Concepts in Computer Architecture

### 3. Translating High-Level Language to Machine Code

### 4. Amdahl’s Law

### 5. Power Wall and Multi Core
