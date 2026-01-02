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
앞선 예시에서 add a, b, c 라는 명령어를 실행 시 a, b, c는 각각 Register를 뜻한다.  
Register는 저장요소를 뜻하며 각각 특정한 값을 저장하고 있다.  

    int a = 10;
    int b = 20;
    int c = a + b;

위와 같은 C code가 Compile되면, 각각 a, b 라는 Register에 10, 20 값이 저장되고, c에는 a와 b라는 Register안에 저장된 10, 20이라는 값이 add연산되어, 30이라는 값이 저장된다.

이번에는 이 RISC-V의 Registers에 대해 알아본다.

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
![Calling Convention for Registers](/assets/img/studies/ca/Computer Architecture/Instructions/3.png){: width="540" height="270"}

이번에는 간단한 C코드 예시를 통해, Register의 Conventional Use에 대해서 알아본다.  

기본적은 Register는 저장공간으로 함수의 변수들의 값들을 저장한다.  

하지만, 32개의 Register만 가지기에, Cache나 DRAM과 같은 Memory에 당장 쓰이지 않는 값이지만 나중에 사용해야 하는 값들을 저장할 필요가 있다.  

Cache나 DRAM과 같은 Memory Access는 performance를 감소시키기에, Memory에 값을 반드시 보존하는 Register들과 굳이 안 보존해도 되는 Register들 2개의 Group으로 나눈다.  

또한, 그 외의 Register들도 특별히 정해진 역할을 수행하는 Register들이 존재한다. 이는 위의 Table에 정리되어 있다.

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
![R-Type](/assets/img/studies/ca/Computer Architecture/Instructions/4.png){: width="360" height="180"}

본격적으로 RISC-V Instructions에 대해서 알아본다.  
Instructions는 32bit binary로 정의되며, 동작에 따라 다양한 Type으로 나뉜다.  

R-type instruction는 두 개의 source register operands를 사용하는  arithmetic operation을 정의한다.  
예를 들어 add 명령어가 이에 해당한다.  
- opcode: 명령어의 연산 코드(operation code)
- rd: 결과가 저장될 destination register operand
- funct3: 추가적인 opcode 필드
- rs1: 첫 번째 source register operand
- rs2: 두 번째 source register operand
- funct7: 추가적인 opcode 필드

참고로, register를 나타내는데 5bit를 사용하는데, 이는 총 32개의 Register가 있기 때문이다.

### 6. RISC-V Instruction Format: I-Type
![I-Type](/assets/img/studies/ca/Computer Architecture/Instructions/5.png){: width="360" height="180"}

**addi를 C code로 비유하면 다음과 같다.**  

    int x = 100;
    x += 12;        // addi x, x, 12 와 정확히 대응

**ld를 C code로 비유하면 다음과 같다.**  

    long *base = array;        // rs1 Register에 배열 시작 주소 저장
    long temp;

    temp = *(base + 3);        // ld x10, 24(x11) 와 대응 (3 * 8 = 24바이트 오프셋)

I형 명령어(I-type instruction)는 constant operand와 하나의 source register operand를 사용하는 연산을 정의한다.  
예를 들어 addi 명령어가 이에 해당한다.  
- I형 명령어는 source register operand가 하나만 있다.
- 2번째 source register operand에 해당하는 5비트는 사용되지 않으므로, 이를 constant offset을 표현하는 데 활용한다. 하지만 5비트로는 -16에서 15까지의 제한된 범위만 표현할 수 있다.
- 따라서, I형 명령어는 funct7과 rs2 부분을 결합하여 12비트 immediate value을 표현한다.
- load 명령어도 I형 형식을 사용한다. (Memory에서 Register로 값을 가져오는 명령어)

### 7. RISC-V Instruction Format: I-Type Variant
![I-Type Variant](/assets/img/studies/ca/Computer Architecture/Instructions/6.png){: width="360" height="180"}

**slli를 C code로 비유하면 다음과 같다.**  

    unsigned int value = 7;     // 7 = 00000111 (2진수)

    value = value << 3;        // slli rd, rs1, 3 와 동일
    // 결과: value = 56 (7 * 2^3 = 56)
    // 2진수: 00111000

- slli, srli, srai 명령어의 경우, doubleword에서 64비트를 초과하여 시프트하는 것은 의미가 없다.
- 따라서 논리 연산의 I형 명령어 형식은 12비트 즉치 필드(immediate field) 중 6비트만 사용한다. (2^6 = 64 이기에)
- 사용되지 않는 나머지 6비트는 funct6 opcode로 재활용된다.

**beq를 C code로 비유하면 다음과 같다.**  

    int x = 5;
    int y = 3;

    if (x == y) {              // beq x10, x11, equal_label 과 대응
        // 여기로 분기됨 (같을 때 실행)
        x = x + 1;
    } 
    else {
        // 같지 않으면 여기 실행 (분기되지 않음)
        y = y + 1;
    }

프로그램 코드의 if-else 문은 compile 시 의사결정을 담당하는 분기(branch) 명령어로 변환된다.  
- beq (branch if equal) 명령어는 x21 Register와 x22 Register의 값이 같을 때 L1이라는 label이 붙은 곳으로 분기한다.
- bne (branch if not equal) 명령어는 x21 Register와 x22 Register의 값이 다를 때 L1이라는 label이 붙은 곳으로 분기한다.  
이러한 명령어들은 조건이 참일 때만 분기가 이루어지기 때문에 conditional branches라고 불린다.

**jalr를 C code로 비유하면 다음과 같다.** 

    int callee() {
        return 42;
        // Assembly Code로 표현 시 :
        // li a0, 42          # 반환값 42를 a0 레지스터에 로드
        // jalr zero, 0(ra)   # ra(반환 주소)로 점프하며 ra에 아무것도 저장 안 함 (zero)
        //                    # 함수 종료 후 caller의 다음 명령어로 복귀
    }

    int caller() {
        int result = callee();  // 함수 호출 → callee의 주소로 점프 
        // Assembly Code로 표현 시 :
        // (jalr ra(ra register 보통 x1), 0(calle함수 주소를 가지는 register))

        // callee가 끝나면 자동으로 여기로 돌아옴 (반환)
        return result + 1;
    }

jalr(Jump and Link Register)는 Register에 저장된 주소로 jump하면서 동시에 return address를 지정된 Register(보통 ra)에 저장한다.  
이는 함수 호출(function call)과 정확히 동일한 동작이다.    

아직 Program Counter의 개념을 다루지 않았지만, Program Counter + 4의 값을 보통 ra에 저장한다.  

jalr zero, 0(ra)의 경우, x0 에 현재 PC + 4 값을 저장하려고 하지만, x0은 hard-wired zero Register이기에, 반환 주소가 저장되지 않고 그냥 ra에 저장된 주소로의 이동만 수행한다.  

### 8. RISC-V Instruction Format: S-Type
![S-Type](/assets/img/studies/ca/Computer Architecture/Instructions/7.png){: width="360" height="180"}

**sd를 C code로 비유하면 다음과 같다.** 

    long value = 42;                   // rs2 레지스터에 저장할 값 (64비트)
    long *base_addr = some_array;      // rs1 레지스터에 베이스 주소 저장 가정

    base_addr[3] = value;
    // => *(base_addr + 3) = value
    // 이는 sd x10, 24(x11) 와 동일 (3 * 8 = 24바이트 오프셋)

S형 명령어(S-type instruction)는 constant operand와 두 개의 source register operand를 사용하는 연산을 정의한다.  
- store 연산은 2개의 source register operand를 필요로 한다.  
- 이 중 1번째 operand는 base address를 저장하고, 2번째 오퍼랜드는 메모리에 저장할 값을 가지고 있다.
- 12비트 immediate field는 두 부분으로 나뉘며, 하위 5비트는 S형에서 사용되지 않는 rd 세그먼트에 배치된다.
- immediate값을 이렇게 분할하는 이유([11:5]와 [4:0]으로 분할)는 rs1과 rs2의 위치를 다른 명령어 형식들과 동일하게 유지하기 위함이다.

### 9. RISC-V Instruction Format: U-Type


### 10. RISC-V Instruction Format: SB-Type


### 11. RISC-V Instruction Format: UJ-Type


### 12. Function Frame and Frame Pointer


### 13. Stack and Stack Pointer


### 14. Program Counter


### 15. RISC-V Addressing Modes


### 16. Big Endian and Little Endian


