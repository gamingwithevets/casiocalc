---
title: A Beginner's Guide to Casio Calculator Hacking
layout: default
---

# A Beginner's Guide to Casio Calculator Hacking
## nX-U8/100 core
The nX-U8/100 core is a special core created by Oki/Lapis Semiconductor (now known as Lapis Technology).
It is the core used by Casio in most of their calculators from the ES series.

You should read the [**nX-U8/100 Core Instruction Manual**](https://github.com/gamingwithevets/nXU8100-resources/blob/main/FEUZ0317A0-U8-INST-05.pdf) before proceeding.

## ROP
Casio calculators have a way to do [**return-oriented programming**](https://wikipedia.org/wiki/Return-oriented_programming) (ROP for short) which,
via the corruption of the stack pointer or the stack itself, allows jumping to any part of code at will.
ROP can be used to read and write to data memory, allowing for dumping the entire calculator ROM.

[Watch this video](https://www.youtube.com/watch?v=IWQ74f2ot7E) to get familiar with the stack.

### Register sizes
The U8 core has 16 byte-sized registers Rn, where n is a number from 0 to 15 and must be divisible by the number of bytes the register can hold.  
They can be combined to create 8 word-sized (2-byte) registers ERn, where the low byte is Rn and the high byte is R(n+1).  
They can also be cominbed to create 4 double word-sized (4-byte) registers XRn and 2 quad word-sized (8-byte) registers QRn.
These registers hold the bytes in the order of the byte-sized registers (XR0's highest byte is R0, then R1, R2, and R3).

### RT vs. POP PC
The U8 core has a very unconventional function system.
Its RT (return) instruction is only used for functions that don't use the BL (branch and link) instruction to call other functions.
For functions that do, there needs to be a PUSH LR (LR = link register) at the start of the function, and a POP PC at the end of it.
The RT instruction **does not** pop from the stack and instead loads from LR, which can point to useless code that may crash the calculator, so it's usually preferred to use a gadget ending in POP PC.

### Address format (LARGE memory model)
When an address is pushed to the stack, it is pushed in the following format:
```
cc bb xa xx
```
(all hex digits. `x` = any hex digit)

These 4 bytes can be converted to this address format:
```
a:bbccH
```
where `x` is the physical code segment number for CSR and `aabbH` is the address for PC.  
On ES PLUS models with version `GY4__X`, `LY-7__`/`LY7__X` and `CY2__X`, code segments 0 and 1 are mirrored to all other code segments.
That means if you jump to, say, code segment 5, you will actually be running code from code segment 1.

### Skipping PUSH LR
As mentioned above, functions that call other functions need to start with a PUSH LR and end with a POP PC.
However, in ROP hackstrings, we usually jump to the instruction *after* PUSH LR.
If we don't jump to after PUSH LR, the POP PC instruction will pop the value of LR pushed at the beginning of the function, which, again, can point to useless and/or unsafe code.
Skipping over PUSH LR guarantees that the hackstring will work properly and not crash the calculator.

### Start position, initial values of ER0 and ER2
The start position of ROP chains can vary between different models and generations.
- On ES ROM 018, the start position is the 49th character.
- On ES PLUS, the start position depends on the initial value of the stack pointer. Subtract the initial SP value by `0x8DB8` to get the start position - 1.
- On CWI/EX, the start position depends on how many bytes are before `an`. In `100an` mode (110 bytes before `an`), the start position is the 35th character.

When executing a hackstring, the initial values of ER0 and ER2 are usually the address of the cache area and input area respectively.
Most functions use ER0 as a pointer, so this information may be helpful for injecting data into the calculator, or spelling.

### POP gadgets
At the end of functions are instructions that pop register values that are backed up from the stack.
Since we jump right to the instruction that does the popping, we can basically set any register to any value at will, no matter the size!
Only one exception: you can't set a register to something that contains a `0x00` byte with popping, for that you'll need to use other gadgets.

Say we have a `POP ERn` gadget at `0:1234H`. In the hackstring you would do: `34 12 x0 xx bb aa`, where `0xaabb` is the new value of ERn.  
Now, say we have a `POP XRn` gadget at `5:6789H`. In the hackstring you would do: `89 67 x5 xx <4 bytes for XRn>`  
Same for `POP QRn`, just that you need to insert 8 bytes instead of 4.  
Finally, note that `POP Rn` will also pop 2 bytes like `POP ERn`, just that the high byte is unused.

### Making a simple ROP hackstring
(fx-580VN X will be used as example. `$` = any character)

In this example, we will make a simple ROP hackstring that draws an 8-pixel line on the bottom left of the screen.

First, you need the necessary gadgets. For this example we will use these gadgets:
```
1:7B34H  POP XR0             4 cos⁻¹( 1 $
1:76D4H  [ER2] = R0, R2 = 0  ⁻¹ ³√( 1 $
```
Then, since we're on CWI, we'll need to figure out the `an` mode to use and what bytes to take.  
We'll use `100an` as an example. For the 8 pixels, you'll need to get a character with hex code `0xFF__` and a character with hex code `0xFFD4`.
They're both invisible so try not to delete them accidentally.

Now, we'll make a mock ROP chain in byte format. Something like this:
```
<34 bytes>
34 7B x1 xx
FF xx E0 FF
D4 76 x1 xx
```
Then convert the bytes to tokens:
```
<34 bytes>
4 cos⁻¹( 1 $
<0xFF__> 𝐆 <0xFFD4>
³√( 1 $
```
And finally, type the hackstring on real hardware! If successful, you should see a small line at the bottom left of the screen, meaning you succeeded.
