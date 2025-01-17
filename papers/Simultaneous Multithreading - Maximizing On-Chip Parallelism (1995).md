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
[2. PROGRAM REPRESENTATION DEPENDENCES AND PARALLEL EXECUTION](#2-program-representation-dependences-and-parallel-execution)  
[3. THE MICRO ARCHITECTURE OF A TYPICAL SUPERSCALAR PROCESSOR](#3-the-microarchitecture-of-a-typical-superscalar-processor)  

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

:--------------------------------------------------------------------------------------------------------------------------------------:

:--------------------------------------------------------------------------------------------------------------------------------------:

:--------------------------------------------------------------------------------------------------------------------------------------:

:--------------------------------------------------------------------------------------------------------------------------------------:

:--------------------------------------------------------------------------------------------------------------------------------------:

### 1. INTRODUCTION
  Simultaneous multithreading (SM) : A technique that permits several independent threads to issue to multiple functional units each cycle.  
  Objective of SM : To increase processor utilization for both long memory latencies and limited available parallelism per thread.  

  Constraints of the early Microprocessors :  

    (1) Superscalar Architecture : 
      Multiple instruction issue has the potential to increase performance, but is ultimately limited by instruction dependencies.  
      Long-latency operations within the single executing thread.  
      -> shown as horizontal waste and vertical waste in Figure 1.  
    
    (2) Multithreaded Architectures : 
      Hides memory and firnctional unit latencies, reducing vertical waste.  
      In any one cycle, these architectures issue instructions from only one thread (single thread in a single cycle).  
      The technique is thus limited by the amount of parallelism.  

    Therefore, Simultaneous multithreading came out resolving both horizontal waste and virtical waste.
  
  Contents of this Paper :  

    (Section 2) Discuss Basic machine model, the workloads for simulation and the simulation environment.  
    (Section 3) Evaluating the performance of a single-threaded superscalar architecture.  
    (Section 4) Discuss performance of a range of SM architectures  
                and compares with superscalar architecture, and fine-grain multithreaded processor.  
    (Section 5) Explores the effect of cache design alternatives on the performance of simultaneous multithreading.  
    (Section 6) Compares the SM approach with conventional multiprocessor architectures.  
<!--   
    (1) Introduce several SM models.  
    (2) Evaluate the performance of those models relative to superscalar and fine-grain multithreading.  
    (3) Show how to tune the cache hierarchy for SM processors.  
    (4) Demonstrate the potential for performance and real-estate advantages of SM architectures over small-scale, on-chip multiprocessors. -->


  
### 2. Basic machine model, the workloads for simulation and the simulation environment



### 3. Evaluating the performance of a single-threaded superscalar architecture.
