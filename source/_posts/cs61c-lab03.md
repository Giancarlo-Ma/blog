---
title: cs61c-lab03
date: 2018-11-22 04:52:27
categories:
- CS61C
tags:
- RISC
- machine structure
---

# Familiarizing yourself with Venus

Task: Paste the contents of `lab3_ex1.s` in Venus and Record your answers to the following questions. Some of the questions will require you to run the RISC-V code using Venus' simulator tab.

What do the .data, .word, .text directives mean (i.e. what do you use them for)? Hint: think about the 4 sections of memory.
<span style="color:red">.data: subsequent items put in user data segment; .word: Store the n 32-bit quantities in successive memory words; .text: subsequent items put in user text segment(machine code)</span>

Run the program to completion. What number did the program output? What does this number represent?

<span style="color:red">34, the number represents the 9th fibonacci number</span>

At what address is `n` stored in memory? Hint: Look at the contents of the registers.

<span style="color:red">at address 0x10000010</span>

Without using the "Edit" tab, have the program calculate the 13th fib number (0-indexed) by manually modifying the value of a register. You may find it helpful to first step through the code. If you prefer to look at decimal values, change the "Display Settings" option at the bottom.

<span style="color:red">We can achieve this by mannualy manipulate the value of counter register t3 to 13</span>

# Translating from C to RISC-V

Task: Find/explain the following components of this assembly file.

The register representing the variable k.
The registers acting as pointers to the source and dest arrays.
The assembly code for the loop found in the C code.
How the pointers are manipulated in the assembly code.
