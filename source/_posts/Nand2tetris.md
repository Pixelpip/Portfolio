---
title: 'My Journey into Hardware: Conquering Nand2Tetris Project 1'
date: 2025-08-15 18:35:19
tags:
  - Nand2Tetris
  - Computer Architecture
  - Hardware
  - Logic Gates
  - HDL
  - Learning
categories:
  - Nand2Tetris
---

I've always been fascinated by the magic that happens inside a computer. How do simple electrical signals turn into the complex applications we use every day? To truly understand this, I decided to take the **Nand2Tetris** course, a journey to build a complete computer system from the ground up.

This week, I successfully completed the first major milestone: **Project 1: Elementary Logic Gates**. The objective is to construct a family of basic logic gates using nothing but the primitive Nand gate. It's a profound exercise that peels back the first layer of abstraction in modern computing.

The project uses a simple Hardware Description Language (HDL) to define the chips. We are given skeleton `.hdl` files, and the task is to implement the internal logic (`PARTS:` section) for each chip and test it using the provided simulator.

It was an amazing "aha!" moment to see complex boolean logic emerge from the composition of simpler parts. Here are my implementations for each of the gates.

### Not

The simplest gate. A NOT is just a NAND with its inputs tied together. If `in` is 0, `Nand(0,0)` is 1. If `in` is 1, `Nand(1,1)` is 0.

```hdl
/**
 * Not gate:
 * out = not in
 */

CHIP Not {
    IN in;
    OUT out;

    PARTS:
    Nand(a=in, b=in, out=out);
}