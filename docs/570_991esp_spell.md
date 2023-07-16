---
title: fx-570/991ES PLUS Spelling
layout: default
---

(from https://tieba.baidu.com/p/2473766810)

NOTE: This method is only tested on fx-991ES PLUS! It should work on fx-570ES PLUS, but no other models are supported! Make sure you have the right model!

0. Reset your calculator
	[SHIFT] [9] [3] [=] [AC]

1. Set it to LineIO
	[SHIFT] [MODE](SET UP) [2]

2. Type in the following formula:
	X=Σ(X,1,1x10^9
	the "x10^" is the key on the right of the decimal point ([.])

3. Press [CALC], then [AC] to break the operation

4. Now modify the formula so it looks like this:
	X=Σ(X,1,2

5. Press [CALC] [=] [right], now your cursor should be stuck.

6. Repeat [sqrt] [frac], do it until the "M" indicator lights up
	[sqrt] is the square root key, [frac] is the fraction key

7. Press [AC] [left], now press [=] 2 times, and then [CALC] 10 times
	The LCD should display something that looks like barcode, don't worry
	You are halfway done here! These steps set up the stack pointer and something else in the memory

8. Press [AC], and then store 0 to variable X by pressing [0] [SHIFT] [RCL](STO) [)](X).

9. Repeat step 2 to step 5.

10. This time, input random characters(around 88 characters) until you see "X=Σ(X,1,2" on the right of your cursor
	Don't worry if you overwrite some of them!
	The position is the only thing that matters here, you can always move your cursor back to the position

11. Now you are in the backup buffer, now you should set your messages up here. The backup buffer is 100 bytes long, which means it can hold 100 characters. Input characters according to the following table:
	<6 random characters, the content doesn't matter>
	<16 characters, line 1 of the message> <a random character>
	<16 characters, line 2 of the message> 9(Yes, it must be a "9")
	<16 characters, line 3 of the message> <a random character>
	<16 characters, line 4 of the message> <23 random characters>
	4 sin( 4 sin(

12. After you've done all of these, press [AC] [left] [=].
	4 lines of ASCII characters should appear on the screen!
	The cursor will be flashing, pressing [SHIFT] [CALC] should make it disappear, but it doesn't work 100% of the time.