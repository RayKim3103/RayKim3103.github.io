---
layout: page
title: Simultaneous Multithreading - Maximizing On-Chip Parallelism (1995)
description: >
  This paper tells about the Implementing Simultaneous Multithreading
hide_description: false
sitemap: false
---

<!-- 0. this unordered seed list will be replaced by toc as unordered list
{:toc} -->

Table of contents

[OverView](#overview)  
[0. Terminology Definition](#0-terminology-definition)  
[1. INTRODUCTION](#1-introduction)  
[2. Basic machine model, the workloads for simulation and the simulation environment](#2-basic-machine-model-the-workloads-for-simulation-and-the-simulation-environment)  
[3. Superscalar Bottlenecks: Where have all the Cycles Gone?](#3-superscalar-bottlenecks-where-have-all-the-cycles-gone)  
[4. Simultaneous Multithreading](#4-simultaneous-multithreading)  
[5. Cache Design for a Simultaneous Multithreaded Processor](#5-cache-design-for-a-simultaneous-multithreaded-processor)  
[6. Simultaneous Multithreading vs. Chip Multiprocessing](#6-simultaneous-multithreading-vs-chip-multiprocessing)  




### OverView
* Terminology Definition
* Introduction
* General problem solved by superscalar processors 
* Specific techniques used in typical superscalar microprocessors 

### 0. Terminology Definition
:--------------------------------------------------------------------------------------------------------------------------------------:

1 - Single-chip multiprocessors (CMP)

  Single-chip multiprocessors : Multiple processor cores on a single silicon chip.  
                                Each core operates as an independent processor, capable of executing processing tasks in parallel.
                                It allows various programs or processes to execute simultaneously.

:--------------------------------------------------------------------------------------------------------------------------------------:

2 - Lockup-free Cache

  Lockup-free Cache : cache design that allows the cache to continue processing other operations even when a cache miss occurs.

:--------------------------------------------------------------------------------------------------------------------------------------:

3 - Hardware Context

  Hardware Context includes :  
  
    Program Counter (PC)  
    Registers
    Status Registers
    TLB
    ...

  Each thread is mapped to each Hardware Context.  (1 to 1 mapping)

  Examples  

    4 Hardware Contexts, 8 Threads:  
      Only 4 threads can be executed, while the remaining 4 threads remain in a waiting state.  
    8 Hardware Contexts, 8 Threads:  
      All 8 threads can be executed simultaneously.  

:--------------------------------------------------------------------------------------------------------------------------------------:

4 - Vertical Waste

  Occurs when a thread enters a waiting state  
  due to memory access, data dependencies, cache misses, or similar reasons.  
  
  Leaving processor resources idle.

:--------------------------------------------------------------------------------------------------------------------------------------:

5 - Horizontal Waste

  Occurs when a single thread cannot fully utilize all the functional units of a processor  
  (e.g., integer execution unit, floating-point unit).  
  
  Resulting in some units remaining idle.

:--------------------------------------------------------------------------------------------------------------------------------------:

6 - Execution Resources

  The hardware components required by a processor to actually execute instructions.  

  Functional Units:

    * Integer Arithmetic Logic Units (Integer ALUs)
    * Floating Point Units (FPUs)
    * Multipliers
    * Load/Store Units

  Issue Slots:  

    Slots responsible for dispatching instructions to functional units.
  
  Pipeline Stages:

    Execution stages of instructions, such as Fetch, Decode, Execute, Memory Access, and Write-back.
  
  Role:

    Execution resources handle tasks like calculations, accessing memory, and moving data,  
    enabling the processor to complete instructions effectively.

:--------------------------------------------------------------------------------------------------------------------------------------:

7 - Non-Execution Resources

  Components that support instruction execution and manage the flow of data and instructions.  
  These resources provide data to execution resources or manage control signals to ensure smooth operation.  

  Cache:  

    Stores frequently accessed data for quick retrieval.  
  
  TLB (Translation Lookaside Buffer):  

    Translates virtual memory addresses to physical memory addresses.  

  Branch Prediction Table:  

    Predicts the outcome of branch instructions to enhance execution speed.  

  Instruction Queue:  

    Stores instructions waiting to be executed.  

  Register File:  

    Holds data required for instruction execution.  

  Role:  

    Supplying data to execution resources, control the flow of instructions, and optimize memory access,  
    ensuring efficient processor operation.  

:--------------------------------------------------------------------------------------------------------------------------------------:

### 1. INTRODUCTION
  Simultaneous multithreading (SM) : 
  
    A technique that permits several independent threads to issue to multiple functional units each cycle.  
  
  Objective of SM : 
  
    To increase processor utilization for both long memory latencies and limited available parallelism per thread.  

  Constraints of the early Microprocessors :  

    (1) Superscalar Architecture : 
      Multiple instruction issue has the potential to increase performance, but is ultimately limited by instruction dependencies.  
      Long-latency operations within the single executing thread.  
      -> shown as horizontal waste and vertical waste in Figure 1.  
    
    (2) Multithreaded Architectures :  
      Use 1 thread for 1 clock cycle.  
      Hides memory and firnctional unit latencies, reducing vertical waste.  
      In any one cycle, these architectures issue instructions from only one thread (single thread in a single cycle).  
      The technique is thus limited by the amount of parallelism.  

    Therefore, Simultaneous multithreading came out resolving both horizontal waste and virtical waste.  
    -> Many threads per 1 clock cycle. But, it has only 1 core.  
  
  Contents of this Paper :  

    (Section 2) Discuss Basic machine model, the workloads for simulation and the simulation environment.  
    (Section 3) Evaluating the performance of a single-threaded superscalar architecture.  
    (Section 4) Discuss performance of a range of SM architectures  
                and compares with superscalar architecture, and fine-grain multithreaded processor.  
    (Section 5) Explores the effect of cache design alternatives on the performance of simultaneous multithreading.  
    (Section 6) Compares the SM approach with conventional multiprocessor architectures.  
  
### 2. Basic machine model, the workloads for simulation and the simulation environment

Simulation Environment  

  Model is based on the Alpha AXP 21164.  

  Copies the execution pipelines, the memory hierarchy, the TLBs, and the branch prediction logic of a wide superscalar processor.  

![simulation_details](/assets/img/papers/2/2_1.png){: width="360" height="180"}

  Details :  

    (1) Typical simulated configuration contains 10 functional units of four types and maximum issue rate of 8 instructions per cycle.  
        (10 units with 4 types : four integer, two floating point, three load/store and 1 branch)  
    (2) Assume that all functional units are completely pipelined.  

  Cache Details :  

    (1) Assume first- and second-level on-chip caches larger than on the Alpha.  
    (2) Multi-ported Cache.  
    (3) An instruction cache access occurs whenever the program counter crosses a 32-byte boundary.  
    (4) Otherwise, the instruction is fetched from the prefetch buffer.  
    (5) Models lockup-free caches and TLBs.  
    (6) When, TLB misses require two full memory accesses and no execution resources.  

  Execution Details :  

    (1) Support limited dynamic execution.  
        -> Independent instructions are issued in-order with "8 inst/thread".  
    (2) Support static scheduling.  
        -> Using the Multiflow trace scheduling compiler.  

  Branch prediction Details :  

    (1) Use 2048-entry, direct-mapped, 2-bit branch prediction history table.  
    (2) Use a 12-entry return stack, to predict return destinations.  
        -> return stack : stack which stores return address.  
    (3) Use 32-entry jump table, which records the last jumped-to destination from a particular address.  
    
  Multithread Details :  

    (1) Assume 8 hardware contexts.  
    (2) Instructions are scheduled in a strict priority order.  
        i.e., context O can schedule instructions onto any available functional unit,  
              context 1 can schedule onto any unit unutilized by context O.

  Pipeline Details :  

    (1) Use basic pipeline to accommodate simultaneous multithreading.
 
  Workload Details :  

    (1) Use uniprocessor applications, assigning a distinct program to each thread.  
        -> To measure the raw instruction throughput achievable by multithreaded superscalar processors.  
    (2) In the single-thread experiments : All of the benchmarks are run to completion using the default data sets.  
    (3) In the multithreaded experiments : 
        -> Total B benchmarks.  
        -> Each runs : T × 500 million instructions.  
        -> For each data sets, Average of B × T × 500 million instructions. (Average of B runs)  
        -> Number of benchmarks(B) is limited to number of Threads. Because, to limit the number of execution.  

    
### 3. Superscalar Bottlenecks: Where have all the Cycles Gone?

Measuring Issue utilization : 

  Measure the unused issue slots which shows how much efficiently processors are used.  

![simulation_results_1](/assets/img/papers/2/2_2.png){: width="480" height="240"}

Above Figure 2. shows that functional units of wide superscalar processor are highly underutilized. (Usage : 19%)  

Also, Figure 2. shows that no dominant source of wasted issue bandwidth.  
Since, there is no dominant cause of wasted cycles, there appears to be no dominant solution.  

  SOLUTIONS That can be used :  

    (1) latency-tolerating technique :  
        But!!!  
        -> Can be used to specific types of latencies. 
      
    (2) Instruction scheduling :  
        But!!!  
        -> Compiler has already achieved most of the available gains in that regard.  
      
    (3) Putting larger amounts of caches into on-chip area :  
        But!!!
        -> Even if memory latencies are completely eliminated, 
            we cannot achieve 40% utilization of this processor.  

Thererfore, general latency-hiding solution is needed to achieve dramatic increase in parallelism.  

**Multithreading** can be the solution!!

### 4. Simultaneous Multithreading

  **4.1. The Machine Models**   

![simulation_results_2](/assets/img/papers/2/2_3.png){: width="480" height="240"}

  Fine-Grain Multithreading  

    Property : 1 thread issues instructions each cycle, but it can use the entire issue width of the processor.  
                Only model that does not feature simultaneous multithreading.  
    Pros     : Hides all sources of vertical waste.  
    Cons     : Does not hide horizontal waste. 

  SM: Full Simultaneous Issue  

    Property : Completely flexible simultaneous multithreaded superscalar.  
                All eight threads compete for each of the issue slots each cycle.  
    Pros     : Shows  potential completely for simultaneous multithreading.  
    Cons     : Least realistic model in terms of hardware complexity.  
  
  SM: Single, Dual, Four Issue  

    Property : These three models limit the number of instructions each thread can issue.  
            -> i.e. In a SM:Dual Issue processor, each thread can issue a maximum of 2 instructions per cycle.  
                    Therefore, a minimum of 4 threads would be required to fill the 8 issue slots in one cycle.  
    Pros     : Less hardware complexity, compare to Full Simultaneous Issue Model.  
  
  SM: Limited Connection

    Property :  Each hardware context is directly connected to exactly one of each type of functional unit. 
            -> i.e. If the hardware supports eight threads and there are four integer units,   
                    each integer unit could receive instructions from exactly 2 threads.  
    Pros     : Less hardware complexity, compare to Full Simultaneous Issue Model.  
    Cons     : Partitioning of functional units among threads. Thus less dynamic than in the other models.  
                But, each functional unit is still shared (the critical factor in achieving high utilization).  
            -> You can see 2 threads are sharing integer unit in the last example.  

  Notice that the fine-grain model may not necessarily represent the cheapest implementation.  

  Many of these complexity issues are inherited from wide superscalar design rather than from multithreading.  

  **4.2. The Performance of Simultaneous Multithreading**

![simulation_results_3](/assets/img/papers/2/2_4.png){: width="360" height="360"}

  *Why SM model increase the Processor Utilization?*  

    1. Reduction of Vertical Waste :  
      SM allows multiple threads to execute simultaneously.  
      If one thread is in a waiting state, other threads can use the idle resources.  
    
    2. Reduction of Horizontal Waste :  
      SM is designed for multiple threads to share processor resources.  
      If one thread does not use certain functional units, another thread can utilize them.  

    3. Dynamic Resource Sharing :  
      SM dynamically shares processor resources, such as issue slots, functional units, registers, and caches, among multiple threads.  
      Resources are dynamically allocated to other threads, reducing idle states and improving throughput.  
    
    4. Utilization of Hardware Contexts :  
      SM activates multiple hardware contexts within a single processor core.  
      Each hardware context is capable of running a separate thread.  
      Increasing the number of hardware contexts allows more threads to run concurrently, thereby improving resource utilization.  
  
    [cf]  

    5. Combination of ILP and TLP in Simultaneous Multithreading :  
      Example:  
        Using only ILP in a superscalar processor:  
          Thread A:  
            A = B + C, D = E + F (executed in parallel using integer execution units).  

          However, if Thread A does not use the floating-point unit, that unit remains idle.  
        
        Combining ILP and TLP in SM:  
          Thread A:  
            A = B + C, D = E + F (using integer execution units).  
          Thread B:  
            G = H * I (using the floating-point unit).  
          
          With SM, Threads A and B execute simultaneously, ensuring all functional units are efficiently utilized.  

  *Cons of SM*  

    See the Figure 3(c), (= the Full Simultaneous Issue model).  

    1. The lowest-priority thread among the 8 threads executes at 55% of the speed of the highest-priority thread.  

       why?  
        Since, competition occurs for issue slots and functional units (Execution Resources).  
      
    2. The highest-priority thread, which is relatively less affected by contention for issue slots and functional units,  
        experiences performance degradation as the number of threads increases (a 35% speed reduction with 8 threads).
      
       why?  
        The impact of sharing other system resources (Non Execution Resources: caches, TLBs, branch prediction table).  
        Due to a decrease in locality.  

### 5. Cache Design for a Simultaneous Multithreaded Processor

![simulation_results_4](/assets/img/papers/2/2_5.png){: width="360" height="360"}

  Simulation Assumption : 

    1. Focuses only on the first-level (Ll ) caches.  
    2. All experiments use the 4-issue model with up to 8 threads.  
    3. The caches are specified as,  
      [Instruction cache size][private or shared]. (unit is KB)  
      [Data cache size][private or shared]. (unit is KB)  

      Example : 
        64p.64s has 8 private 8 KB inst. caches and a shared 64 KB data cache.  
  
Simulation Result :  

  Example : 

    * the 64s.64s cache ranks first among all models at 1 thread and last at 8 threads.  
    * the 64p.64p cache gives nearly the opposite result.  

  Notice that the tradeoffs are not the same for both instructions and data.  
    
    1. A shared data cache is better than a private data cache over all numbers of threads.  
      (e.g., compare 64p.64s with 64p.64p) 

    2. Instruction caches benefit from private caches at 8 threads.  
    
    why? 
      * Private I caches eliminate conflicts between different threads in the I cache.  
        Example:  
          For Shared I Cache:
            Assume that the instructions to be executed by Thread A and Thread B are mapped to the same cache block.
            When Thread A uses that block, Thread B may overwrite it, causing a cache conflict.
            -> As a result, the instructions required by Thread B must be fetched from memory again, leading to a cache miss.
          
          For Private I Cache:
            Threads A and B use their own independent I caches.
            When Thread A fetches instructions from its private I cache, it does not affect Thread B’s cache.
            -> As a result, no conflicts occur, cache misses are reduced, and performance improves.
      
      * Shared D cache allows a single thread to issue multiple memory instructions to different banks.  
        Example:
          Thread A issues three memory instructions:
            Instruction 1 accesses cache bank 1,
            Instruction 2 accesses cache bank 2,
            Instruction 3 accesses cache bank 3.
          As a result, the shared cache supports parallel memory accesses from Thread A, increasing the throughput.

  Two Configurations That Seem to Be Good Choices:

    64s.64s Configuration:

      Performance differences among 8 threads are minimal.  

      It makes the cost of optimizing for a small number of threads relatively low.  

      This appears to be an attractive option.  
    
    64p.64s Configuration:

      If most thread slots are expected to be fully occupied, 
        this configuration delivers the best performance in such scenarios.  
      
      Additionally, it consistently maintains at least second place in performance 
        when the number of threads is small, without significant degradation.  

### 6. Simultaneous Multithreading vs. Chip Multiprocessing

![simulation_results_4](/assets/img/papers/2/2_6.png){: width="480" height="240"}

**Overview of Simultaneous Multithreading vs. Chip Multiprocessing**

  The key difference :  

    The way those resources are partitioned and scheduled :  
      -> The multiprocessor statically partitions resources, assigning a fixed number of functional units to each thread.  
      -> The SM processor allows the partitioning to change every cycle.  
        Clearly, scheduling is more complex for an SM processor.  
    
    => However, other areas the SM model requires fewer resources, relative to multiprocessing.  

**SM and MP configurations for simulation**

  For most of the comparisons, set them equal: 
    -> The number of register sets (i.e, Number of threads for SM and the number of processors for MP).  
    -> Total issue bandwidth.  
    -> Specific functional unit configuration.  
    -> 8 KB private instruction and data caches (per thread for SM, per processor for MP).  
    -> 256 KB 4-way set-associative shared second-level cache.  
    -> 2 MB direct-miipped third-level cache.  
  
  Evaluation conditions:  
    -> MPs with 1, 2, and 4 issues per cycle on each processor.  
    -> SM processors with 4 and 8 issues per cycle.  

**NOTICE of SM model**
  SM results may be optimistic in two respects  
    (1) Amount of time to schedule instructions onto functional units.  

      In SM, there is a possibility of additional scheduling time, but experiment show that its impact is minimal.  

      Impact of Increased Cycles:  
        Experimental results: If instruction issue delay increases by 1 cycle.  
        - Performance impact in single-thread mode is less than 1%.  
        - When 8 threads are running, the performance reduction is negligible, at less than 0.5%.  
      
    (2) Amount of time to access Shared cache.  

      -> Multiprocessor with private caches and private load/store units, can minimize the distances between them.  
      -> SM processor cannot do so, even with private caches, because the load/store units are shared.  

      SOLUTION :  
        1. Having eight load/store units (one private unit per thread, associated with a private cache).  
           It still allows to match MP performance with fewer than half the total number of MP functional units (32 vs. 15). 
        
        2. 4 load/store units and 8 threads, sharing a single cache-load/store combination among each set of 2 threads. 

**Simulation Result**

----------------------------------------------------------------------------------------------------------------------------

  Tests A, B, and C:  

    The number of functional units (FUs) effectively set to unlimited in each test.  
      i.e. All types of functional units are made available for each issue slot.  

    The number of "register sets" and the "total issue bandwidth" are kept constant across all experiments.  
      i.e. In Test C, both a 4-thread, 8-issue SM processor and a 4-processor MP with 2 issues per processor   
           have 4 register sets and can issue up to 8 instructions per cycle.  

    Since these models have a high functional unit-to-thread-to-issue bandwidth ratio, both configurations utilize most of the issue bandwidth.  
  
    However, Simultaneous Multithreading (SM) achieves this more effectively. (Less Resources)  

----------------------------------------------------------------------------------------------------------------------------

  Test D:  

    Test A is repeated, but the SM processor is restricted to a more realistic configuration.  
    -> A 10-functional-unit configuration, as used in other experiments in the paper, is adopted.  
    
    Even in this setup, SM outperforms MP by nearly the same margin as in Test A.  
    The SM configuration uses 22 fewer functional units than MP while also requiring fewer forwarding connections.

----------------------------------------------------------------------------------------------------------------------------

  In Tests E and F, MP is allowed a significantly higher total issue bandwidth.

  Test E:
    Each MP processor can issue 4 instructions per cycle, resulting in a total issue bandwidth of 32 for 8 processors.
    For SM, each thread can issue 4 instructions per cycle, but 8 threads share 8 issue slots.
    Despite this imbalance in issue slots, the results are similar for both configurations.
  
  Test F:
    A 4-thread, 8-issue SM processor slightly outperforms a 4-processor MP, where each processor has 4 issues.
    While MP has a total issue bandwidth of 16 instructions per cycle, which is twice the bandwidth of SM, SM still achieves better performance.
    This result highlights the limitation of MP, where each processor is restricted to issuing 4 instructions per cycle, reducing overall efficiency.
  
----------------------------------------------------------------------------------------------------------------------------