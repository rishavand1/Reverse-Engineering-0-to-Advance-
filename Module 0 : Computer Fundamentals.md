# Module 0: Computer Fundamentals

## Duration

2 Weeks

## Difficulty

Beginner

## Overview

Before learning reverse engineering, students must understand how a computer executes software.

Many beginners immediately start using Ghidra or x64dbg without understanding what happens when a program launches. This module explains the fundamental concepts that power modern software execution.

After completing this module, students should understand:

* How CPUs execute instructions
* How memory stores data
* How programs are loaded into memory
* How processes and threads work
* How operating systems communicate with software
* Why stack overflows and memory bugs occur

---

# Learning Objectives

By the end of this module, students should be able to:

* Explain how a CPU executes instructions.
* Describe the role of registers.
* Explain how RAM stores program data.
* Distinguish between stack and heap memory.
* Explain what a process is.
* Explain what a thread is.
* Understand virtual memory concepts.
* Understand system calls at a high level.
* Identify process memory regions.

---

# Prerequisites

None.

This is the starting module.

---

# Theory

# Section 1: Computer Architecture

## What Is Computer Architecture?

Computer architecture describes how hardware components work together to execute software.

<img width="481" height="329" alt="zsiwLrERT0AjQDRmtDQXBigZChd5fcS9GxNQFPObrqWeqeP1ltmQ-q_63KGu7pvEZMSgkToLhOahd2vaMllda7zWbYPzddLkfck-QMDh_Ev7olfnsrRAdwAGJsg98glxcVMIMyf0BDUQds4nqfDxEoU9mL_wUfYanFrUQUYpDzc" src="https://github.com/user-attachments/assets/10e4a14c-1285-4668-be9b-8c49ac548bf6" />


A computer consists of:

```text
Program
   ↓
CPU
   ↓
Memory
   ↓
Operating System
```

When you launch a program:

1. Operating system loads it.
2. CPU fetches instructions.
3. Instructions execute.
4. Data is read from memory.
5. Results are written back.

---

## Real World Example

You double-click:

```text
notepad.exe
```

Windows:

1. Loads executable into memory.
2. Creates a process.
3. Creates a thread.
4. Gives control to CPU.
5. Program begins execution.

---

# Section 2: CPU Basics

## What Is a CPU?

CPU stands for:

```text
Central Processing Unit
```

The CPU executes instructions.

Examples:

```text
ADD
SUB
MOV
CALL
RET
```

Every software application eventually becomes machine instructions executed by the CPU.

---

## CPU Components

### Arithmetic Logic Unit (ALU)

Performs:

* Addition
* Subtraction
* Comparisons

### Control Unit

Controls execution flow.

### Registers

Small storage locations inside CPU.

---

## Practical Example

```c
int a = 5;
int b = 3;
int c = a + b;
```

CPU performs:

```text
Load 5
Load 3
Add
Store Result
```

---

# Section 3: Memory Basics

## What Is Memory?

Memory stores:

* Instructions
* Variables
* Program state

Most commonly:

```text
RAM
```

---

## Why Memory Exists

CPU is fast.

Storage is slow.

Programs must be loaded into RAM before execution.

---

## Memory Analogy

```text
CPU = Chef

RAM = Kitchen Counter

SSD = Storage Room
```

Chef works from counter.

Not directly from storage room.

---

# Section 4: Registers

## What Are Registers?

Registers are tiny memory locations inside CPU.

Fastest storage available.

Examples:

```text
RAX
RBX
RCX
RDX
RSP
RBP
RIP
```

---

## Why Important For Reverse Engineering?

Most debugger windows display:

```text
RAX
RBX
RCX
RDX
```

Understanding registers is essential for debugging.

---

## Example

```asm
MOV RAX, 10
MOV RBX, 20
ADD RAX, RBX
```

Result:

```text
RAX = 30
```

---

# Section 5: Instruction Execution

## Fetch-Decode-Execute Cycle

CPU repeats:

```text
Fetch
↓
Decode
↓
Execute
↓
Repeat
```

---

## Example

Instruction:

```asm
ADD RAX, RBX
```

CPU:

1. Fetches instruction.
2. Decodes operation.
3. Adds values.
4. Stores result.

---

# Section 6: Cache Memory

## What Is Cache?

Cache stores frequently used data.

Purpose:

Reduce memory access time.

---

## Cache Levels

```text
L1 Cache
L2 Cache
L3 Cache
RAM
```

Speed decreases downward.

---

## Why Reverse Engineers Care

Performance analysis.

Understanding memory access patterns.

Malware analysis.

---

# Section 7: Virtual Memory

## Problem

Programs need more memory than physically available.

Solution:

Virtual Memory.

---

## Concept

Every process believes it owns:

```text
0x00000000
to
0xFFFFFFFF
```

Reality:

Operating system maps virtual memory to physical memory.

---

## Benefits

* Isolation
* Security
* Stability

---

# Section 8: Processes

## What Is a Process?

A running program.

Examples:

```text
chrome.exe
notepad.exe
explorer.exe
```

Each process has:

* PID
* Memory
* Threads
* Handles

---

## Example

Opening Chrome:

```text
chrome.exe
```

Creates a process.

---

# Section 9: Threads

## What Is a Thread?

Execution unit inside process.

---

## Example

Chrome Process

```text
Process
 ├── Thread 1
 ├── Thread 2
 ├── Thread 3
```

Multiple tasks execute simultaneously.

---

# Section 10: Memory Layout

Typical process memory:

```text
High Memory
-------------
Stack
-------------
Heap
-------------
Data
-------------
Code
-------------
Low Memory
```

---

# Section 11: Stack vs Heap

## Stack

Stores:

* Function calls
* Local variables
* Return addresses

Fast.

Automatically managed.

---

## Heap

Stores:

* Dynamic memory
* User allocated memory

Slower.

Manually managed.

---

## Example

```c
int x = 5;
```

Stack.

```c
malloc(100);
```

Heap.

---

# Section 12: System Calls

## What Is A System Call?

Programs cannot directly access hardware.

Instead they ask operating system.

---

## Example

Open file:

```c
fopen("data.txt");
```

↓

System Call

↓

Operating System

↓

Disk Access

---

## Common System Calls

```text
Open
Read
Write
Close
CreateProcess
```

---

# Practical Demonstration

## Demonstration 1

Launch Notepad.

Observe:

* Process creation
* Memory allocation
* Thread creation

Tool:

Process Hacker

---

## Demonstration 2

Launch Calculator.

Observe:

* PID
* Threads
* Memory usage

---

## Demonstration 3

View CPU usage.

Observe:

* Processes
* Threads
* Resource consumption

---

# Labs

## Lab 1

Explore Running Processes

### Goal

Understand processes.

### Tasks

* Open Process Hacker
* Identify chrome.exe
* Identify explorer.exe
* Record PID

---

## Lab 2

Observe Threads

### Goal

Understand threads.

### Tasks

* Open Chrome
* Count threads
* Record findings

---

## Lab 3

Memory Observation

### Goal

Observe memory usage.

### Tasks

* Open applications
* Compare RAM usage
* Record observations

---

# Challenge

## Challenge 1

Investigate:

chrome.exe

Questions:

* Process ID?
* Thread Count?
* Memory Usage?

Provide screenshots.

---

# Knowledge Check

1. What is a CPU?
2. What are registers?
3. Difference between RAM and Cache?
4. What is virtual memory?
5. Difference between process and thread?
6. Difference between stack and heap?
7. What is a system call?

---

# Additional Resources

Books:

* Computer Systems: A Programmer's Perspective
* Operating Systems: Three Easy Pieces
* Windows Internals

Videos:

* Computer Architecture Basics
* Operating System Fundamentals

---

# Module Summary

In this module we learned:

* CPU fundamentals
* Memory fundamentals
* Registers
* Instruction execution
* Cache
* Virtual memory
* Processes
* Threads
* Memory layout
* Stack vs Heap
* System calls

These concepts form the foundation for reverse engineering, debugging, malware analysis, and vulnerability research.
