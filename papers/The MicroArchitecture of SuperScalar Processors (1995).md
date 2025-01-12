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

## OverView
* Terminology Definition
* Introduction
* General problem solved by superscalar processors 
* Specific techniques used in typical superscalar microprocessors 
* Recent superscalar processors 
* Future directions for instruction level parallelism

## Terminology Definition
#### 1. Instruction Level Parallelism (ILP)
  Instruction Level Parallelism (ILP): the degree that a processor can execute multiple instructions in parallel.  
  It aims to improve performance by executing independent instructions simultaneously.

  * ILP Implementation methods

    1) Pipelining: Divides instructions into multiple stages and processes each stage of different instructions concurrently.  
    2) Superscalar Architecture: Uses multiple execution units to process multiple instructions in parallel.  
    3) Out-of-Order Execution: Executes instructions that are ready first, regardless of their program order.  
  
  * ILP example

    a = b + c; // Instruction 1

    d = e + f; // Instruction 2

    => we can execute Inst.1 & Inst.2 in parallel because they are independent.

#### 2. Dynamic instruction scheduling
  Dynamic Instruction Scheduling : a processor dynamically adjusts the execution order of instructions at *"runtime"*.  
  It resolves bottlenecks caused by data or control dependencies between instructions.

#### 3. Tomasulo's Algorithm
  Algorithm that optimizes instruction execution by utilizing dynamic instruction scheduling and out-of-order execution.

#### 4. Vector Processing
  Vector Processing handles multiple data simultaneously using a vector, which is a collection of data elements.  
  Unlike scalar processing, where one data element is processed at a time, vector processing allows a single instruction to process multiple data elements at once.

#### 5. Binary compatibility
  Binary compatibility : the ability to execute a machine program written for an earlier generation processor.  

#### 6. Precise state
  Precise state : the ability of a computer system to accurately track and record the state of the instructions being executed.  
  This allows the system to recover to a known state if an error or interruption occurs.

#### 7. Static Program
  Static Program : all components, such as the code, data, and variables, are determined at compile time and remain unchanged during execution.  

## Introduction
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

  Therefore, SuperScalar processing should maintain the feature of instruction set compatibility and a sequential execution model.

  The requirement of Parallel processing in perspective of Hardware

    1) Instruction fetch strategies that simultaneously fetch multiple instructions.  
    2) Methods for determining true dependences & communicating register values.  
    3) Methods for initiating, or issuing, multiple instructions in parallel.  
    4) Resources for parallel execution.  
    (i.e. multiple pipelined functional units, memory hierachies)  
    5) Methods for communicating data values through memory.  
    6) Methods for  maintain an outward appearance of sequential execution.  
  
## PROGRAM REPRESENTATION, DEPENDENCES, AND PARALLEL EXECUTION

