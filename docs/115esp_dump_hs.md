---
title: fx-115ES PLUS ROM Dump Hackstring
layout: default
---

This page lists the hackstring used to dump the real ROM of the fx-115ES PLUS. I don't have the actual dumped ROM yet, however it's pretty cool to see the hackstring used.

*Hackstring author unconfirmed, possibly user202729*
```
======== dump_render ========
Program:
    set LR
    pop src, render
    halt
Bytes:
    E6 3F ?0 ??
    66 3B ?0 ??
    ss ss
    ?? ?? ?? ?? ?? ?? ?? ??
    ?? ?? ?? ??
    FE FE ?1 ??
Hackstring:
    <54 characters>
    cv16    RndFix(    0    $
    yhat    cs25    0    $
    [source address]
    $    $    $    $    $    $    $    $
    $    $    $    $
    cv40    cv40    1    $
    <20 characters>

    'RndFix(' = '=' + 2
    'yhat' = 'Abs(' + 3
```