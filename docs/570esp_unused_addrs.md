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
0268E
02B2E
03DD6
03DDE
04146
04150
04588
04748
0477C
04CDC
04CDE
05A34
05B2C		# draw sqrt
05B86
05D0C
05E6A
05EBC		# draw d/dx
06002
0640C
06456		# on *printing* integrate in math mode
069F4
06A58
06D0A
06D20
076C2
0794A
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
09136
092E0
09552
0958A
095C2
09712
09BBC
0A4E4
0AA50
0ACA6
0AF70
0B1E2
0B56C
0B6B6
0B7CE
0C22E		# on boot
0C25A
0C692
0C6DE
0C728
0C74C
0C77A
0C7A8		# on backspace
0C7C2
0C806		# navigating boxes of int
0C836
0C86A		# cmplx
0C874		# enter lineio
0C8AE
10564
10C0A
111E0		num_nthroot
112B0
1195C
11A9C
11B18
11BA6
11C36
11C7E
11CBA
11CF8
11D32
11D94
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
12DA0
12DD2
12E02
12E32
12E68
12E98
12EAE
12EE4
12F18
12F2E
12F50
12F66
12F94
12FC4
12FF4
13024
1305A
13070
13086
1309C
130B2		num_stat_P
13152		num_stat_Q
1316C		num_stat_R
13190		num_stat_to_t
131E8
131EA
131EC
131EE
1333A		num_randint
13404		mat_add
134BE		mat_sub
13532		mat_div
13542
136BE
1373C		mat_det
139E4
13B4A
141E8
1423E
14430
14776
1477A
14792
14796
1479A
147C4
148B4		cmplx_rnd
148CC		cmplx_negate
148E0
1491C
14958
1499C
14A7E		num_cmplx_pow
14AB2		num_add
14ABE		num_sub
14ACA		num_mul
14AD6		num_div
14B08		cmplx_inverse
14B1C		cmplx_add
14B30		cmplx_sub
14B44		cmplx_mul
14BA8		cmplx_div
14BBC
14BD6		num_frac
14CD8		num_pol
14CE0		num_rec
14D00		num_percent
14D12		num_deg
14D16		num_rad
14D1A		num_grad
14E86		num_tan
15672		num_sum__     # variable x is restored here
158C6
15A42
15AFE
15B64
15B96
15C30
15CAC
16D2C
171D2
171E6
17268
1768A
1794C
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
1A5A6		num_factorial
1A5BA
1A5CE
1A61E		num_10xp
1A65A		num_log10
1A834		num_atanh
1A846		num_acosh
1A858		num_asinh
1A86A		num_tanh
1A87C		num_cosh
1A88E		num_sinh
1A8B2		num_acos
1A8C4		num_asin
1ADB6
1AF44
1B4A0
1B502
```