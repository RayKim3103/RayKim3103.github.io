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

## OverView of this paper
* Introduction
* General problem solved by superscalar processors 
* Specific techniques used in typical superscalar microprocessors 
* Recent superscalar processors 
* Future directions for instruction level parallelism

## Terminology Definition
1. Instruction Level Parallelism (ILP)

  Instruction Level Parallelism (ILP) refers to the degree to which a processor can execute multiple instructions in parallel.

  It aims to improve performance by executing independent instructions simultaneously.

  ILP Implementation methods
    1) Pipelining: Divides instructions into multiple stages and processes each stage of different instructions concurrently.

    2) Superscalar Architecture: Uses multiple execution units to process multiple instructions in parallel.

    3) Out-of-Order Execution: Executes instructions that are ready first, regardless of their program order.
  
  ILP example

    a = b + c; // Instruction 1

    d = e + f; // Instruction 2
    
    => we can execute Inst.1 & Inst.2 in parallel because they are independent.

2. Dynamic instruction scheduling

  Dynamic Instruction Scheduling is a technique where a processor dynamically adjusts the execution order of instructions at "runtime" to improve performance.

3. Tomasulo's Algorithm
4. Vector Processing
5. Multi Processing
6. Binary compatibility
7. Precise state

## Introduction