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

## Introduction