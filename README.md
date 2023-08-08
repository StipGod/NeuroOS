# NeuroOS

# Kernel Design Document for x86

## 1. Introduction

### 1.1 Purpose
This document outlines the design and architecture for a basic kernel targeting x86 architecture, primarily intended for educational and research purposes.

### 1.2 Scope
Our kernel aims to manage memory, handle basic I/O, and schedule tasks. It'll be minimalist, prioritizing clarity over complexity.

## 2. System Overview

Our kernel, named `SimpleX86`, will function as a monolithic kernel. It will handle interrupt management, task scheduling, and basic I/O operations.

## 3. Kernel Initialization

### 3.1 Entry Point
Post-bootloader, execution starts at `kernel_main`, which initializes all subsystems and enters the scheduler.

### 3.2 Stack Setup
A static stack space will be allocated for the kernel. It'll be initialized in the assembly before jumping to `kernel_main`.

### 3.3 Video Output Initialization
We'll use VGA text mode at address `0xB8000`. The kernel will provide basic functions like `kprint()` for debugging.

### 3.4 Higher Half Loading
The kernel will be loaded into higher memory, above `0xC0000000`, making kernel-space memory management simpler.

## 4. Memory Management

### 4.1 Physical Memory Management
- Memory detection: We'll use BIOS interrupts during early boot to determine available memory.
- Memory mapping: A bitmap will represent available and reserved memory pages.

### 4.2 Paging
- Directory and tables: A static page directory and tables will be used at first.
- Page frame allocator: A First-Fit allocation strategy will allocate and deallocate pages.

### 4.3 Kernel Heap
A basic `kmalloc` and `kfree` system will be implemented using linked list allocation.

## 5. Interrupts and I/O

### 5.1 Interrupt Descriptor Table (IDT)
We'll set up a static IDT to handle 256 potential interrupts, with handlers for timer and keyboard interrupts initially.

### 5.2 Keyboard Input
Interrupt `0x21` will handle keyboard input. A ring buffer will temporarily store keystrokes.

### 5.3 Timer
The PIT (Programmable Interrupt Timer) will be set up to tick every 10ms, enabling basic task preemption.

## 6. Task Scheduling

### 6.1 Context Switching
Using the x86 `pusha` and `popa` instructions, we'll save and restore register states during a context switch.

### 6.2 Task States and Process Table
Tasks will have states like `READY`, `RUNNING`, and `BLOCKED`. A simple array will function as our process table.

### 6.3 System Calls
Using interrupt `0x80`, we'll handle system calls. Initially, this will cover task creation and termination.

## 7. Development Environment

### 7.1 Tools and Technologies
- Cross-Compiler: GCC targeting our kernel architecture.
- Emulator: QEMU for testing.
- Bootloader: GRUB, to simplify booting.

### 7.2 Building and Running
A Makefile will automate the build process. The output kernel image can be run in QEMU or loaded onto a USB drive for real hardware testing.

## 8. Future Work

- Implement a basic file system.
- Improve memory management with demand paging.
- Implement user mode and basic security measures.
