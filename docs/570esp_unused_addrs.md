---
title: fx-570ES+ VerE Unused Functions
layout: default
---

This page contains a list of addresses in the `GY454X VerE` real ROM that follow a `POP PC`, `RT`, `RTI` or `BAL` instruction but never get called or jumped to.

This list was created with the help of my nX-U8/100 disassembler [PyU8disas](https://github.com/gamingwithevets/pyu8disas), which is still in active development. Therefore **the list may not be 100% accurate** and **may get updated over time**.

user202729 also labeled (and even used) some of these unused functions, obviously with no idea they are actually unused.

```
005C2
0063C
03DD6
03DDE
04146
05A34
05D0C
05EBC		# draw d/dx
06456		# on *printing* integrate in math mode
069F4
06A58
06D20
076C2
08E0A
08E18
08E26
08E34
08E3C
08E44
08E4C
08E56
08E5E
08E66
08E6E
08E76
08E7A
08E86
09050
09070
09090
09552
0A4E4
0B1E2
0C22E		# on boot
0C25A
0C692
0C6DE
0C728
0C77A
0C7A8		# on backspace
0C7C2
0C806		# navigating boxes of int
0C836
0C86A		# cmplx
0C8AE
10564
10C0A
111E0		num_nthroot
112B0
11C7E
11CF8
11D9C
11DA4
11DAC
11DB4
11DBC
12C3A
12C4C
12C68
12CB6
12CE0
12D1E
12D5A
12DD2
12E02
12E32
12E98
12EAE
12F2E
12F50
12F66
12FC4
12FF4
13024
13070
13086
1309C
130B2		num_stat_P
13152		num_stat_Q
1316C		num_stat_R
13190		num_stat_to_t
131E8
13532		mat_div
13542
136BE
1373C		mat_det
1423E
1477A
14CE0		num_rec
14D16		num_rad
14D1A		num_grad
171D2
171E6
17960
1796A
17974
1797E
18F84
1A4EE		num_log
1A502		num_combi
1A516		num_permu
1A53E
1A5A4
1A5BA
1A5CE
1A61E		num_10xp
1A65A		num_log10
1A846		num_acosh
1A858		num_asinh
1A86A		num_tanh
1A87C		num_cosh
1A88E		num_sinh
1A8B2		num_acos
1A8C4		num_asin
1ADB6
```