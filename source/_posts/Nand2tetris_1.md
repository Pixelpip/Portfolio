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

***

## AND
This is a simple gate the produces the output of 1 when both the inputs to the gate is 1 in all other cases the output produced remains 0.

Implementing the gate just using Nand gates is quite simple too , just take the inputs a and b into the Nand gate once then apply the Nand gate on the output produced by the first gate.

Below is the Nand2tetris HDL implementation of the gate.

```hdl

CHIP And {
    IN a, b;
    OUT out;
    
    PARTS:
    Nand(a=a , b=b , out=out1);
    Nand(a=out1, b=out1, out=out);
  
}

```

## NOT

The is the simplest gate. A NOT is just a NAND with the same input applied across both the ends of the gate together. If `in` is 0, `Nand(0,0)` is 1. If `in` is 1, `Nand(1,1)` is 0.

```hdl

CHIP Not {
    IN in;
    OUT out;

    PARTS:
    Nand(a=in, b=in, out=out);
}
```

## OR

OR gate in boolean algebra acts as a gate for adding two boolean numbers. It produces an output of 0 when both the inputs are zero and 1 in all other cases.

To build a OR gate just Nand gate we will have to use 3 Nand gates two Nand gates with inputs being a and b respectively what this effectively does is convert the inputs into each of its complement and the complements are then fed into the 3rd Nand gate which will produce the output of a+b or a OR b.

```hdl

CHIP Or {
    IN a, b;
    OUT out;

    PARTS:
    Nand(a=a , b=a , out=nota);
    Nand(a=b , b=b , out=notb);
    Nand(a=nota, b=notb , out=out);
}

```

## XOR

Xor is a gate that produces a output of one when the values of a and b are different i.e if a is 1 and b is 0 or a is 0 and b is 1 the output is 1 in all other cases the output is 0.

```hdl 

CHIP Xor {
    IN a, b;
    OUT out;

    PARTS:
    Not(in=a,out=nota);
    Not(in=b , out=notb );
    And(a=a , b=notb , out=aAndNotb );
    And(a=nota , b=b , out=bAndNota );
    Or(a=aAndNotb , b=bAndNota , out=out );
}

```


