---
title: fx-115ES+ Diagnostic Code
layout: default
---

Below is the diagnostic mode's code found in the real fx-115ES PLUS ROM.

*Disassembly provided by LifeEmu (`lifeemu`)*

```nasm
namespace ?os
	?render			:=	03B5Ch
	?LCD_fill		:=	07008h
	?LCD_box		:=	0705Eh
	?LCD_checkb1		:=	07016h
	?LCD_checkb2		:=	07036h
	?diag_line_print	:=	06FF8h

	?curAble		:=	080DDh
	?formulaXcor		:=	08112h

	?magicStr		:=	0860Eh

end namespace

; diagnostic (goes to black screen)
; 06EFCh on real 115+
; 07044h on 115+ emulator
; In:
;	None
; Out:
;	None
; Destroys:
;	ER0, ER2, EA
; Notes:
;	This version of code is from REAL 115+.
diagnostic:
	push	lr
	push	xr4
	push	qr8

	add	sp,	-4
	mov	fp,	sp

; invalidate magic string
	lea	os.magicStr
	mov	er0,	0
	st	er0,	[ea+]
	st	er0,	[ea+]

; disable cursor?
	mov	r0,	1
	st	r0,	[os.curAble]

	mov	r4,	1

	mov	r0,	0FFh
	bl	os.LCD_fill	; black screen

	mov	r0,	00h
	bl	os.LCD_fill	; white screen

	bl	os.LCD_box	; box pattern
	bl	os.LCD_checkb1	; checkboard 1
	bl	os.LCD_checkb2	; checkboard 2

	bl	070A4h		; checksum?

	mov	r0,	0
	bl	0F15Ch		; contrast?
	mov	r0,	1
	bl	0C392h		; keyboard test?
	bl	06F8Ch		; clear screen?

	mov	r2,	93h
	mov	r3,	2Dh	; char* str = 0x2D93
	mov	r0,	1	; row = 1
	bl	os.diag_line_print

	mov	r2,	9Bh
	mov	r3,	2Dh	; char* str = 0x2D9B
	mov	r0,	15	; row = 15
	bl	os.diag_line_print

	mov	r2,	8Ah
	mov	r3,	2Dh	; char* str = 0x2D8A
	mov	r0,	22	; row = 22
	bl	os.diag_line_print

	bl	os.render	; render

	mov	er0,	fp
	bl	0EC5Eh

	l	r0,	[os.formulaXcor]
	push	r0
	bl	0EB48h
	pop	r0
	st	r0,	[os.formulaXcor]

	bl	053A6h
	bl	03F88h

	add	sp,	4
	pop	qr8
	pop	xr4
	pop	pc
```
