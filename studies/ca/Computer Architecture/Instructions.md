---
layout: page
title: Computer Instructions
description: >
  This page tells about the Computer Instructions
hide_description: false
sitemap: false
---

Table of contents

[OverView](#overview)  
[1. Instructions and Von Neumann Architecture](#1-instructions-and-von-neumann-architecture)  
[2. CISC vs. RISC](#2-cisc-vs.-risc)  
[3. RISC-V Registers](#3-risc-v-registers)  
[4. Calling Convention for Registers](#4-calling-convention-for-registers)  
[5. RISC-V Instruction Format: R-Type](#5-risc-v-instruction-format:-r-type)  
[6. RISC-V Instruction Format: I-Type](#6-risc-v-instruction-format:-i-type)  
[7. RISC-V Instruction Format: I-Type Variant](#7-risc-v-instruction-format:-i-type-variant)  
[8. RISC-V Instruction Format: S-Type](#8-risc-v-instruction-format:-s-type)  
[9. RISC-V Instruction Format: U-Type](#9-risc-v-instruction-format:-u-type)  
[10. RISC-V Instruction Format: SB-Type](#10-risc-v-instruction-format:-sb-type)  
[11. RISC-V Instruction Format: UJ-Type](#11-risc-v-instruction-format:-uj-type)  
[12. Function Frame and Frame Pointer](#12-function-frame-and-frame-pointer)  
[13. Stack and Stack Pointer](#13-stack-and-stack-pointer)  
[14. Program Counter](#14-program-counter)  
[15. RISC-V Addressing Modes](#15-risc-v-addressing-modes)  
[16. Big Endian and Little Endian](#16-big-endian-and-little-endian)

### OverView
* Instructions and Von Neumann Architecture
* CISC vs. RISC
* RISC-V Registers
* Calling Convention for Registers
* RISC-V Instruction Format: R-Type
* RISC-V Instruction Format: I-Type
* RISC-V Instruction Format: I-Type Variant
* RISC-V Instruction Format: S-Type
* RISC-V Instruction Format: U-Type
* RISC-V Instruction Format: SB-Type
* RISC-V Instruction Format: UJ-Type
* Function Frame and Frame Pointer
* Stack and Stack Pointer
* Program Counter
* RISC-V Addressing Modes
* Big Endian and Little Endian

### 1. Instructions and Von Neumann Architecture
![Von Neumann Architecture](/assets/img/studies/ca/Computer Architecture/Instructions/1.png){: width="360" height="180"}

1. Instructions  
    컴퓨터 언어의 단어는 명령어(instructions)라고 불린다.  
    컴퓨터 언어의 어휘는 명령어 집합(instruction set)이며, 그 설계는 명령어 집합 구조(Instruction Set Architecture, ISA)라고 한다.  
    명령어 집합의 목표는 하드웨어 사용을 단순화하는 것이다.  

1. Von Neumann Architecture  
  현대 컴퓨터는 폰 노이만 아키텍처(Von Neumann architecture)를 기반으로 하며, 이는 stored-program 개념을 구현한다.  
    - 컴퓨터 프로그램은 메모리에 저장된다.  
    - 처리 장치(예: CPU)는 메모리에서 프로그램을 불러와 실행한다.  
    - 프로그램의 결과는 다시 메모리에 기록된다.  

### 2. CISC vs. RISC
![CISC vs. RISC](/assets/img/studies/ca/Computer Architecture/Instructions/2.png){: width="540" height="270"}

2. Complex Instruction Set Computer, CISC  
    작업을 가능한 한 적은 수의 어셈블리 명령어 라인으로 완료하는 것을 목표로 한다.
    이를 위해 프로세서 하드웨어가 복잡한 연산을 직접 이해하고 실행할 수 있도록 설계한다.

2. Reduced instruction set computers, RISC  
    동일한 작업을 완료하기 위해 단순한 명령어를 사용한다.
    하드웨어를 단순하게 만들어 축소된 명령어를 빠르게 실행한다.
    기본적인 RISC-V 명령어는 다음과 같은 형식을 가진다.
    - add a, b, c  
      - add는 operation type이다.
      - a, b, c는 operands이다.
      - 이 명령어는 b와 c의 값을 읽어 들인 후 덧셈을 수행 후, 결과를 a에 저장한다.
      - 즉, 이 명령어는 a = b + c를 수행한다.

### 3. RISC-V Registers
RISC-V Register의 크기는 64비트이며, 이를 더블워드(doubleword)라고 한다.
32비트로 표현되는 데이터의 크기는 워드(word)라고 한다.

- RISC-V는 x0부터 x31까지 이름 붙여진 32개의 64비트 Register를 정의한다.  
- 3개의 operand를 사용하는 명령어는 결과를 저장할 Register 하나를 선택한다.  
- 나머지 2개는 이전 명령어들이 생성한 기존 Register다.

- 왜 RISC-V는 Register를 32개만 가지는가?  
    Register는 명령어가 작업할 데이터를 빠르게 공급하기 위한 것이다.  
    Register가 많으면 필요한 데이터를 찾기 위해 Register를 검색하는 데 더 많은 시간이 걸린다.  
    그러나 Register가 적으면 작업 공간(workspace)의 크기가 줄어든다.

### 4. Calling Convention for Registers
![CISC vs. RISC](/assets/img/studies/ca/Computer Architecture/Instructions/3.png){: width="720" height="360"}

// 호출자 함수 (caller)
int caller() {
    int temp1 = 10;  // x10~x17 (caller-saved) 사용 가능
    int temp2 = 20;  // 임시 변수
    int result = callee(temp1, temp2);  // callee 호출
    return result;  // temp1, temp2는 callee가 망가뜨려도 괜찮음
}

// 피호출자 함수 (callee)
int callee(int a, int b) {  // x10, x11에 매개변수 전달됨
    static int saved_var = 100;  // x18~x27 (callee-saved) 사용
    // saved_var 값은 함수 호출 전후로 유지되어야 함
    
    int local = a + b;  // x5~x7 (caller-saved) 임시 사용
    return local * saved_var;
}

- 모든 Register를 저장하고 복원하는 부담을 피하기 위해 RISC-V는 Register를 두 그룹으로 분류한다.  
- x5~x7, x10~x17, x28~x31은 피호출자 함수(callee)가 보존할 필요가 없다.  (caller-saved registers 또는 temporary registers)  
- x8~x9, x18~x27은 피호출자 함수(callee)가 반드시 보존해야 한다.  (callee-saved registers 또는 saved registers)  

### 5. RISC-V Instruction Format: R-Type


### 6. RISC-V Instruction Format: I-Type


### 7. RISC-V Instruction Format: I-Type Variant


### 8. RISC-V Instruction Format: S-Type


### 9. RISC-V Instruction Format: U-Type


### 10. RISC-V Instruction Format: SB-Type


### 11. RISC-V Instruction Format: UJ-Type


### 12. Function Frame and Frame Pointer


### 13. Stack and Stack Pointer


### 14. Program Counter


### 15. RISC-V Addressing Modes


### 16. Big Endian and Little Endian


