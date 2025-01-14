---
layout: page
title: The MicroArchitecture of SuperScalar Processors (1995)
description: >
  This paper tells about the introduction to SuperScalar Processors
hide_description: false
sitemap: false
---

<!-- 0. this unordered seed list will be replaced by toc as unordered list
{:toc} -->

Table of contents

[OverView](#overview)  
[0. Terminology Definition](#0-terminology-definition)  
[1. INTRODUCTION](#1-introduction)  
[2. PROGRAM REPRESENTATION DEPENDENCES AND PARALLEL EXECUTION](#2-program-representation-dependences-and-parallel-execution)  
[3. THE MICRO ARCHITECTURE OF A TYPICAL SUPERSCALAR PROCESSOR](#3-the-microarchitecture-of-a-typical-superscalar-processor)  

### OverView
* Terminology Definition
* Introduction
* General problem solved by superscalar processors 
* Specific techniques used in typical superscalar microprocessors 

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

  Why should we care about *Dependences* to parallelize the instruction.  
  Because, Instructions that are independent can execute simultaneously.

  **[Dependences]**

  1) Control dependences

    why should we care about *control dependences*?

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

  **[Instruction Fetch]**

  The instruction fetch phase of superscalar processing supplies instructions to the rest of the processing pipeline.  

  *Instruction Cache* is used to contain instructions.  
  Cache is used because it is faster than Memory.  
  But, Hit and Miss is the problem. When, Cache misses, it have to search Memory to find content.  
  Thus, Cache Miss increases latency.  

  For a superscalar implementation to sustain the execution of multiple instructions per cycle, the fetch phase must be able to fetch multiple instructions per cycle.  
  The number of instructions fetched per cycle should at least match the peak instruction decode and execution rate and is usually somewhat higher.  

    For example, there should be margin when Instruction Cache misses.  
    For example, when branch Inst. transfers control to an instruction in the middle of a cache line, 
      then only the remaining portion of the cache line contains useful instructions. So, loss of bandwidth occurs.  

  To smoothen this problems *Instruction Buffer* is used.  
  It stores Instruction and fetches this Instructions when stall occurs.  

  **[Branch Prediction]**

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
  
  **[SOLUTION : Register Renaming]**

  => physical storage elements are differ with logical storage elements  

  *2 register renaming methods*

<!-- ![Execution Tuple](/assets/img/papers/1/1_1.png){: width="270" height="120" style="float: right; margin-left: 60px;"} -->
![freelist](/assets/img/papers/1/1_1.png){: width="270" height="120"}

    1 -  Using a freelist  
    
    Needs physical register file larger than the logical register file.  
    freelist : list of available physical register.  
    mapping table : mappes logical register and physical register.  

    When an instruction is decoded, its logical result register is assigned a physical register from a free list.  
    In the process, the physical register is removed from the free list.  

    In rename operation, the instruction’s source register designators are used to look up their current physical register names in the mapping table.  
    These are the locations from which the source operand values will be read.  
    Then, destination register is assigned another physical register from a freelist.  
    Free the physical register that was assigned to source register.   

![reorder buffer](/assets/img/papers/1/1_2.png){: width="360" height="180"}

    2 - Using reorder buffer

    Reorder Buffer :  FIFO storage, implemented in hardware as a circular buffer with head and tail pointers.  
    mapping table : mappes logical register and physical register.  

    As instructions complete execution, their result values are inserted into the reorder buffer using mapping table.  
    At the time an instruction reaches the head of the reorder buffer, if it has completed execution,  
    its entry is removed from the buffer and its result value is placed in the register file.  

#### C. Instruction Issuing and Parallel Execution 

  In Instruction Decoding execution tuples are created and buffered.  
  The next step is to determine which tuples can be *issued* for execution.  

  Instruction issue is defined as the run-time checking for availability of data and resources.  

  Ideally an instruction is ready to execute as soon as its input operands are available.  

  But, there are some constraints.  
    1) execution units, interconnect, and register file (or reorder buffer) ports.  
    2) organization of buffers.  
  
  The instruction issue buffer can be designed in various ways to keep instructions in an executable state and optimize parallel processing.  

  Below are three main approaches to achieving this.  

![issue queue](/assets/img/papers/1/1_3.png){: width="480" height="240"}

    1) Single Queue Method  
       No out-of-order issuing, register renaming is not required.  
       Operand availability can be managed via simple reservation bits assigned to each register.  
       A register is reserved when an instruction modifying the register issues. (Since, It should be not be inturrpted)  
       The reservation is cleared when the instruction completes.  
    
    2) Multiple Queue Method  
      Instructions issue from each queue in order, but the queues may issue out of order.  
      Register Renaming may be used in a restricted form. (i.e. Register Renaming only for store/load Inst.)  
      
    3) Reservation Stations  
      Instructions may issue out of order; there is no strict FIFO ordering.  
      Use Register Renaming.  

      **Prcedure**

      Instruction is dispatched to the reservation station.  
      Available operand values are read from the register file and placed in the reservation station.  
      The operand designators of unavailable data wait!  
      When the result designators complete instructions reservation station logic compares the value.  
      (value of result designators vs. value of operad designators)
      When there is a match, the result value is putted into reservation station.  
      When all the operands are ready in the reservation station, the instruction may issue.  

#### D. Handling Memory Operations 

  **[Memory Hierarchy]**

  To reduce the latency of memory operations, memory hierarchies are used.  
  All processors today contain a data cache, and it is rapidly becoming commonplace to have multiple levels of data caching.  

  **[Address Calculation]**

  It is not possible to identify the memory locations until after the issue phase.  
  Thus, address calculation & address translation is required after the issue phase.  
  (Translation lookaside buffer(TLB) is used to speed up address translation)  

  Fortunately, Address translation and memory access can be parallelized.  

  Memory operations also need to execute as fast as possible.  

  This requires: 

    1 - reducing the latency of memory operations.  
    2 - executing multiple memory operations at the same time.  
    3 - overlapping the execution of memory operations with nonmemory operations.  
    4 - possibly allowing memory operations to execute out-of-order.  
  
  **[SOLUTION to achieve above requirements]**

    1) keep information only for a currently active subset of the memory locations.  
    2) multiported memory hierarchy which allows multiple memory requests to be serviced simultaneously. 

      Multiporting can be achieved by having :  

      1 - multiported storage cells.  
      2 - multiple banks of memory.  
      3 - making multiple serial requests during the same cycle.  

    3) the memory hierarchy must be nonblocking.  

      The key to allowing memory operations to be overlapped :  
      -> it should ensure that hazards are properly resolved, sequential execution semantics are preserved.

      How?  
      -> Use Store address buffer  
        Store buffer contains addresses of all pending store operations.  
        An operation is issued to the memory.  
        Then the store buffer is checked to see if there is a pending store to the same address.  

  Miss handling status registers (MHSR's)

    MHSR is used to track the status of outstanding cache misses, and allow multiple requests to the memory hierarchy to be overlapped.  

    ① Tracking Cache Miss Requests  
      The Miss Handling Status Register (MHSR) stores the status of ongoing cache miss requests.

    ② Preventing Requests requiring same data address
      Prevents requests for the same data address.  
      i.e. if one instruction requests a specific memory address and another instruction requests the same data,
            the second request waits until the initial request is completed.

    ③ Supporting Parallel Processing of Other Requests
      Enables the execution of other instructions while handling a cache miss.  
      Through the MHSR, requests unrelated to the data being awaited can be processed in parallel.  
      This ensures the processor continues operating without stalling.  

    ④ Notifying Waiting Instructions  
      When the requested data becomes ready, the waiting instructions are notified, allowing them to resume execution.  

#### E. Committing State 
  Commit or the Retire phase
  -> Effects of the instruction are allowed to modify the logical process state.  

  The purpose of this phase is to implement the appearance of a sequential execution model.  
  Thus, recovering *"Precise State"* is required.  

  2 ways to recover precise state

    1 - history buffer or a checkpoint  
      When a precise state is needed, it is recovered from the history buffer.  
      In commit phase, get ridding of history state that is no longer required is done.  
    
    2 - Using reorder buffer
      Separate the state of the machine into two
      1) implemented physical state.
        -> physical state is updated immediately as the operations complete.  
      2) logical (architectural) state.  
        -> architectural state is updated in sequential program order as the speculative status of operations is cleared.  

        Reorder buffer : stores the speculative state, control and interrupt information.

        To commit an instruction, the result in the reorder buffer move into the architectural register file (and to memory in the case of a store).  
        Then, Reorder buffer is freed.  

        Control and interrupt information is used to adjust the logical-to-physical mapping table so that the mapping reflects the correct precise state.  

#### F. The Role of Software 
  Software can assist creating a binary.
  
    -> Make the instruction fetching process more efficient.   
    -> Make the instruction issuing and execution process more efficient.  
  
  How?

    1) Increasing the likelihood that a group of instructions can be issued simultaneously.  
      -> By, Scheduling(arranging) instructions statically.  
        (Make group of instructions in the static program matches the parallel execution constraint.)  
    2) Decreasing the likelihood that an instruction has to wait for a result of a previous instruction.
      Need to Consider :  
      1 - Dependence relationships.  
      2 - Resource availability.  
      -> By, arranging producer code to come first, and consumer code to come next.  
    
    => Above 2 methods can be accomplished by static scheduling.
