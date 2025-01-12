---
layout: page
title: The MicroArchitecture of SuperScalar Processors (1995)
description: >
  This paper tells about the introduction to SuperScalar Processors
hide_description: false
sitemap: false
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

### OverView
* Terminology Definition
* Introduction
* General problem solved by superscalar processors 
* Specific techniques used in typical superscalar microprocessors 
* Recent superscalar processors 
* Future directions for instruction level parallelism

### 0. Terminology Definition
1 - Instruction Level Parallelism (ILP)

  Instruction Level Parallelism (ILP): the degree that a processor can execute multiple instructions in parallel.  
  It aims to improve performance by executing independent instructions simultaneously.

  **ILP Implementation methods**

    1) Pipelining: Divides instructions into multiple stages and processes each stage of different instructions concurrently.  
    2) Superscalar Architecture: Uses multiple execution units to process multiple instructions in parallel.  
    3) Out-of-Order Execution: Executes instructions that are ready first, regardless of their program order.  
  
  **ILP example**

    a = b + c; // Instruction 1

    d = e + f; // Instruction 2

    => we can execute Inst.1 & Inst.2 in parallel because they are independent.

2 - Dynamic instruction scheduling

  Dynamic Instruction Scheduling : a processor dynamically adjusts the execution order of instructions at *"runtime"*.  
  It resolves bottlenecks caused by data or control dependencies between instructions.

3 - Tomasulo's Algorithm

  Algorithm that optimizes instruction execution by utilizing dynamic instruction scheduling and out-of-order execution.

4 - Vector Processing

  Vector Processing handles multiple data simultaneously using a vector, which is a collection of data elements.  
  Unlike scalar processing, where one data element is processed at a time, vector processing allows a single instruction to process multiple data elements at once.

5 - Binary compatibility

  Binary compatibility : the ability to execute a machine program written for an earlier generation processor.  

6 - Precise state

  Precise state : the ability of a computer system to accurately track and record the state of the instructions being executed.  
  This allows the system to recover to a known state if an error or interruption occurs.

7 - Static Program

  Static Program : all components, such as the code, data, and variables, are determined at compile time and remain unchanged during execution.  

### 1. INTRODUCTION
  Instruction level parallelism was performed using pipelining.  
  But, The pipeline initiation rate remained at one instruction per cycle for many years.  
  This was a serious bottleneck. Thus, High Performance Processing is required.  

  2 ways for High Performance  

    1) reduce individual instruction latencies.  
    2) execute more instructions in parallel.  

  The way to *execute more instructions in parallel* is **SuperScalar processing**.

  SuperScalar processing : the ability to initiate multiple instructions during the same clock cycle.  
  However, most processor designs inherit a legacy from their predecessors. (Binary compatibility)  

  Sequential execution model : the way processors were implemented many years ago.  
  In this model, one instruction is executed at a time in a sequential manner.  
  Also, sequential model has a concept of precise state.

  Therefore, SuperScalar processing should maintain the feature of *instruction set compatibility* and a *sequential execution model*.

  The requirement of Parallel processing in perspective of Hardware

    1) Instruction fetch strategies that simultaneously fetch multiple instructions.  
    2) Methods for determining true dependences & communicating register values.  
    3) Methods for initiating, or issuing, multiple instructions in parallel.  
    4) Resources for parallel execution. (i.e. multiple pipelined functional units, memory hierachies)  
    5) Methods for communicating data values through memory.  
    6) Methods for  maintain an outward appearance of sequential execution.  
  
### 2. PROGRAM REPRESENTATION, DEPENDENCES, AND PARALLEL EXECUTION
  Application begins as a high level language.  
  Then, compiled into static machine level program.  
  (high level language -> assembly code -> machine code)  

  Assembly code is executed sequentially by incrementing Program Counter (PC).  
  As a result, *Dependences* between instruction occurs.  

  Why we should care about *Dependences*  to parallelize the instruction.  
  Because, Instructions that are independent can execute simultaneously.

  **Dependences**

  1) Control dependences

    why we should care about *control dependences*?

    Instructions is executed sequentially before the branch instruction appears.  
    Let's say Instructions is divided by branch Inst. and let's call each divided Inst. as basic block.  
    Instructions in the basic block will be executed eventually.  
    Then we can execute them simultaneously if there is no *data dependences*.  

    Control dependences is caused by branch inst.  
    To get more parallelism the basic block should be larger.  

    We use *branch prediction* to overcome control dependences.
    
  2) Data dependences

    As, the instructions may access the same storage location, *Data dependences* occur.  

    *True dependences*  

      read-after-write (RAW) : writing value into a register and reading value from the same register.

    *Artificial dependences*  

      write-after-read (WAR) : reading value from a register and writing the value in the same register.  
      write-after-write (WAW) : writing value to a register and writing the value in the same register.  
  
  After resolving control dependences and artificial dependences, instructions are issued and begin execution in parallel.  
  But, instructions does not complete their execution in sequential order since they are parallelized.  
  As a result, instructions must be held in a temporary status until the architectural state can be updated.  
  Meanwhile, to maintain high performance, these results must be usable by dependent instructions.  

  Eventually, when it is determined that the sequential model would have executed an instruction, its temporary results are made permanent by updating the architectural state.  
  This process is typically called *committing or retiring* the instruction.  

### 3. THE MICROARCHITECTURE OF A TYPICAL SUPERSCALAR PROCESSOR

#### A. Instruction Fetching and Branch Prediction

  **Instruction Fetch**

  The instruction fetch phase of superscalar processing supplies instructions to the rest of the processing pipeline.  

  *Instruction Cache* is used to contain instructions.  
  Cache is used because it is faster than Memory.  
  But, Hit and Miss is the problem. When, Cache misses, it have to search Memory to find content.  
  Thus, Cache Miss increases latency.  

  For a superscalar implementation to sustain the execution of multiple instructions per cycle, the fetch phase must be able to fetch multiple instructions per cycle.  
  The number of instructions fetched per cycle should at least match the peak instruction decode and execution rate and is usually somewhat higher.  

  For example, there should be margin when Instruction Cache misses.  
  For example, when branch Inst. transfers control to an instruction in the middle of a cache line, then only the remaining portion of the cache line contains useful instructions. So, loss of bandwidth occurs.  

  To smoothen this problems *Instruction Buffer* is used.  
  It stores Instruction and fetches this Instructions when stall occurs.  

  **Branch Prediction**

  In branch instructions, Delays occur because of redirecting the flow of control.  
  Therefore predicting the Branch result is needed.  
  
  1) Recognizing Conditional Branches  
    All instruction types information is held in the instruction cache along with the instructions.  
    There is predecode logic prior to the instruction cache which generates predecode bits.  

  2) Determining the Branch Outcome  
    Conditional branch can be predicted using one of several types of branch prediction methods.  

    1 - predictors using static information  
      i.e. certain opcode types might more often result in taken branches than others

    2 - predictors using dynamic information ( information that becomes available as the program executes)  
      It use information regarding the past history of branch outcomes.  
      branch history is saved in a *branch history table* or *branch prediction table*.  
      i.e. If Branch is taken increase counter, else decrease counter. Seeing the counter value, predict the Branch outcomes.  

  3) Computing Branch Targets  
    In most architectures, branch targets are relative to the program counter and use an offset value held in the instruction.  
    Branch target buffer which holds the target address that was used the last time the branch was executed makes computing targets faster.
  
  4) Transferring Control  
    When there is a taken (or predicted taken) branch there is at least a clock cycle delay in recognizing the branch.  

    Solutions  
      1 - use the instruction buffer with its stockpiled instructions to mask the delay.  
      2 - use *delayed branches* method (branch does not take effect until the instruction after the branch).  

#### B. Instruction Decoding, Renaming, and Dispatch 
  During this phase, instructions are removed from the instruction fetch buffers.  
  Then, Control and Data dependence linkages are set up.

  Job of the decode phase : to set up one or more execution tuples for each instruction  
  
  What is execution tuple?

  1) It contains an operation to be executed.  
  2) the identities of storage elements where the input operands reside.  
  3) locations where the instruction's result must be placed.

  To increase parallelism, it should overcome WAR & WAW dependences during dynamic execution.  
  
  **SOLUTION : Register Renaming**

  physical storage elements are differ with logical storage elements  

  *2 register renaming methods*

    1 -  Using a freelist  
    
    Needs physical register file larger than the logical register file.  
    freelist : list of available physical register.  
    mapping table : mappes logical register and physical register.  

![Execution Tuple](assets/img/papers/1/1_1.png){:.lead width="150" height="75" style="float: right; margin-left: 15px;"}
    
