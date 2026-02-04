# Single-Stage Pipeline Register (SystemVerilog)

This repository contains a solution for a **Single-Stage Pipeline Register** implemented in SystemVerilog. The module is designed to interface between an upstream source and a downstream sink using a standard **valid/ready handshake** protocol.

## 📄 Problem Statement

**Task:** Implement a single-stage pipeline register in SystemVerilog using a standard valid/ready handshake.

**The Logic:**
* The module sits between an input and output interface.
* It accepts data when `in_valid` and `in_ready` are asserted.
* It presents stored data on the output with `out_valid`.
* It must correctly handle **backpressure** without data loss or duplication.
* The design must be fully synthesizable and reset to a clean, empty state.

## 🛠️ Solution Overview

The solution implements a standard valid/ready pipeline stage. It utilizes a data register to store the payload and a control register (`valid_reg`) to track the state of the pipeline.

### Key Features
* **Protocol:** AXI-stream style Valid/Ready Handshake.
* **Backpressure Handling:** Zero data loss during downstream stalls.
* **Throughput:** Capable of 100% throughput (1 transaction per clock cycle).
* **Reset:** Asynchronous active-low reset ensures a clean initial state.

## 🚀 Implementation Logic

The solution was achieved by breaking the problem into two critical control paths:

### 1. The "Ready" Logic (Upstream Control)
To prevent data loss, the module signals it is ready to accept new input (`in_ready`) under two conditions:
* **The Pipeline is Empty:** We have space to store new data.
* **The Downstream is Ready:** Even if we are full, if the downstream module is asserting `out_ready`, the current data will leave in the same cycle that new data arrives (flow-through).

```systemverilog
assign in_ready = (!valid_reg) || out_ready;
