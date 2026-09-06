---
layout: post
title: CNN Accelerator in RTL
date: 2025-06-20
description: >
  A basic CNN inference accelerator designed in Verilog RTL and brought to
  hardware on FPGA with Vivado — my Intelligent System Design term project.
image:
  path: /assets/img/blog/pawel-czerwinski-848z7lbCjoo-unsplash.jpg
  srcset:
    1920w: /assets/img/blog/pawel-czerwinski-848z7lbCjoo-unsplash.jpg
    960w:  /assets/img/blog/pawel-czerwinski-848z7lbCjoo-unsplash@0,5x.jpg
    480w:  /assets/img/blog/pawel-czerwinski-848z7lbCjoo-unsplash@0,25x.jpg
accent_image: /assets/img/blog/pawel-czerwinski-848z7lbCjoo-unsplash.jpg
excerpt_separator: <!--more-->
sitemap: false
---

Term project for **Intelligent System Design** (Yonsei University, EEE — 2025 Spring).
The goal was to design a basic convolutional neural network inference accelerator
in Verilog RTL, verify it, and implement it on an FPGA using Vivado.

<!--more-->

* toc
{:toc}

## Overview

- **Course:** Intelligent System Design, 2025 Spring
- **Language / tools:** Verilog RTL, Vivado (synthesis, place & route, bitstream)
- **Target:** FPGA <!-- TODO: board / part number, e.g. Zynq-7020 -->
- **Scope:** convolution + activation datapath for CNN inference
- **Source:** [GitHub — \[Undergraduate\]_Intelligent_System](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Intelligent_System)

## Motivation

*(TODO: which network / layer shapes the accelerator targets, and why a
custom datapath instead of a software baseline.)*

## Architecture

*(TODO: block diagram and a short description of each unit.)*

- **PE array** — *(TODO: dimensions, MAC precision, weight/activation bit widths)*
- **Dataflow** — *(TODO: weight-stationary / output-stationary / row-stationary)*
- **On-chip buffers** — *(TODO: input / weight / partial-sum buffer sizes)*
- **Control FSM** — *(TODO: tiling / loop ordering, handshake with the buffers)*

## Implementation notes

*(TODO: key RTL modules and any non-obvious design decisions — fixed-point
format, pipeline depth, how padding / stride are handled, etc.)*

## Verification

*(TODO: testbench strategy — golden vectors from a Python/C reference model,
per-layer comparison, coverage.)*

## Synthesis & implementation (Vivado)

*(TODO: fill in from the Vivado reports.)*

| Metric            | Value      |
|-------------------|------------|
| LUT               | *(TODO)*   |
| FF                | *(TODO)*   |
| DSP               | *(TODO)*   |
| BRAM              | *(TODO)*   |
| Fmax / clock      | *(TODO)*   |
| Latency per infer | *(TODO)*   |

## Results

*(TODO: measured throughput / latency, accuracy vs. the reference model,
and what you would change next time.)*

## Links

- [Project source on GitHub](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Intelligent_System)
