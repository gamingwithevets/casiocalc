---
title: fx-115ES PLUS ROM Dump Hackstring
layout: default
---

*Written by LifeEmu (`lifeemu`) (reformatted)*

======== dump_render ========  
Description:
- Copies the data memory 0:ssss to the screen.

Notes:  
- This one is for fx-115ES PLUS (`LY727X Ver.A`), *NOT* for 2nd edition.  
- This won't work on other CASIO calculators without modification/rewriting, due to addresses of functions are changed.  
- Due to the nature of `render` function, the first 12 bytes will be copied to VRAM area which will be displayed in status bar, so be warned.  
- To read the data, discard the status bar area, and read from left to right, from up to down; Each line consists of 12x8 pixels, a dark pixel is '1', and an inactive pixel is '0'. There are 31 lines, so you can get 12x31 =372 bytes of data(starting from `0:ssss +0Ch`) on the screen.

Program:
```
    set LR
    pop src, render
    halt
```

Bytes:
```
    E7 3F ?0 ??
    66 3B ?0 ??
    ss ss
    ?? ?? ?? ?? ?? ?? ?? ??
    ?? ?? ?? ??
    FE FE ?1 ??
```
Hackstring:
```
    <54 characters>
    cv17    RndFix(    0    $
    yhat    cs25    0    $
    [source address]
    $    $    $    $    $    $    $    $
    $    $    $    $
    cv40    cv40    1    $
    <20 characters>
```
    'RndFix(' = '=' + 2
    'yhat' = 'Abs(' + 3