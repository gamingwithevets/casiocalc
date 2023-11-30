---
title: ClassWiz QR format
layout: default
---

Original document by happy sheep. Translated by Steveyboi with Google Translate + DeepL Translate

<details open markdown="block">
<summary>
    Contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# ClassWiz QR Functional Analysis and Internal Data Storage Principles
{: .no_toc }

Casio launched the ClassWiz series of calculators in 2014, which for the first time implemented a QR code (two-dimensional code) display on a function machine.
Scanning the two-dimensional code jumps to Casio's WES web site, where you can graph function images, histograms, and other charts using exported data, realizing the innovation of a scientific calculator + a handheld device = a graphing calculator.

The QR feature was initially only available on ClassWiz flagship models, and was subsequently extended to non-flagship models, but domestic models have never had access to the feature.
As a result, not much has been done to analyze this feature in China, and it has only been covered in some ClassWiz review posts.
This document will try to analyze and introduce this feature and the meaning of the encoded content in a more systematic way, and welcome criticisms and corrections if there is insufficient information.

Since the data encoded in QR codes are essentially the computational data of the calculator, this document also deals with the principle of storing data inside the calculator.
Fortunately, there is a relatively in-depth study on this topic in the fx-es(ms) bar, so I would like to reintroduce it here as well.

![A Casio fx-JP900 and fx-JP900CW showing 2 different version 11 QR codes.](/casiocalc/images/classwiz_qr_1.png)

When the exported data is too large, the data will be "split" into multiple QR codes, and the numbers in the lower-right corner indicate the current number and the total number of QR codes.
Press [↓], [=], [EXE], or [OK] to display the remaining QR codes in order.
You cannot press [↑] to return to the previous QR code, but you can cycle through them.

QR code segmentation is a part of the QR code ISO standard.
When creating a segmented QR code sequence, a single QR code encodes the order and total number of QR codes in addition to the data segmented into that segment.
Just about no code scanning software we use today is adapted to this split QR code implementation, and will only scan the data of a single QR code, in which case it is necessary to scan the code sequentially and manually merge the data.
Casio has proprietary code scanning software that is adapted to this standard, which is also displayed when scanning codes, so that the complete scanning result can be obtained directly.

![2 phone screenshots showing the process of scanning a QR code on an official Casio app.](/casiocalc/images/classwiz_qr_2.png)

The "version" of the QR code display can be set in Settings, with support for "version 11" and "version 3", the default is version 11.
"Version" is a term used in the QR code specification.
In ClassWiz, it is understood that version 3 QR code elements are larger and can be scanned more easily, but the information carried by a single QR code is also smaller.
However, the information carried by a single QR code is also smaller.
Therefore, the same amount of data would need to be split into more QR codes using version 3.
However, the QR code standard supports splitting the data into a maximum of 16 QR codes, so if the amount of data is too large, version 3 QR codes cannot be displayed and "version 3 not supported" will be displayed.

![2 pictures of a Casio fx-JP900CW, one is showing a version 3 QR code, the other is displaying the error "Not Supported (Version 3)".](/casiocalc/images/classwiz_qr_3.png)

When displaying QR codes, both CW I and CW II support the use of the [←][→] keys to adjust contrast, and the contrast value set here is independent of other screens and is retained when the calculator is reset.
CWII also adds an adjustable contrast icon to the lower right corner.

In addition, pressing [5] when a QR code is displayed can cause the mask pattern of the QR code to change, which is never mentioned in the ClassWiz manual.
The QR code implementation specification defines 8 different mask patterns, mainly to prevent large white or black blocks from appearing in the QR pattern to improve readability.

![A table describing various different QR code mask patterns. (Translated)](/casiocalc/images/classwiz_qr_4.png)

## QR code content overview
After the introduction of the QR function, let's look at how QR encodes data. For example, a QR obtained when the fx-JP900CW is in the Mode menu and the cursor is in the **"Calculate"** mode produces the following after scanning the code:
```
http://wes.casio.com/ncal/index.php?q=I-031A+U-000000000000+M-X100000000+S-1A93A
```

As you can see, there is a link with the host name http://wes.casio.com.
This is Casio's "Worldwide Education Service" web site, which until 2022 processed encoded calculation data and returned it to the display on the back end.
After 2022, Casio upgraded the service so that accessing this link redirects 302 to the classpad.net website, where richer computing functions can be used.

If it's the CW I series, the domain name in the link is followed by /math, while for CW II it's /ncal, which is probably an abbreviation for **n**ew **cal**culator.
Then the link follows /index.php?q=...... The content after q= is the exported calculation data.

The data is divided into sections, each of which is connected by a + sign; the identifiers of the different sections are indicated by a single macro name, and the identifiers are connected to the stored data by a - sign.
Depending on the state of the calculator at the time of the QR, the data in the corresponding section will be reflected.
After analyzing the data, the following is a preliminary explanation of the different identifiers:

| Identifier | Guessed name | Explanation
|--|--|--
| I | ID | Calculator model and ROM version
| U | Unique | The serial number displayed during self-test
| M | Mode | Data such as mode codes, calculation result display methods, etc. stored to indicate how to decode other data
| S | Setting | Stores data such as calculator setting parameters
| R | Result | The result area data, generally consists of two parts, each part is 20 in length
| E | Expression | Generally encoded computational expressions, in algorithm mode it is the created algorithm
| T | Tabulation | Spreadsheet data in Spreadsheet mode, or list data in Statistics, Distribution, Math Box, etc. modes
| C | Chart | Specifically for storing matrix/vector data, also used in Equation mode
| G | g(x) | Expression of g(x) in Table mode
| P | Parameter | Table range parameters set to generate function table
| V | Variable | Occurs when there are variables in the expression of f(x) or g(x), and the value of each variable is stored, except for the x and Ans variables
| Q | / | Exclusive to CW II models, similar to R, but with 28 degrees of separation per part and used only in normal calculations

The following is a more detailed introduction to each part of the data

## I data
The I data stores the internal model number of the calculator, which is the code shown at self-check, and the ROM version.
For example, 243F is shown as CY-243 VerF.
As an Easter egg here, the CW I manual includes a screenshot of a QR code example, which is scanned to give the I data as 23B-.
Character tables may vary slightly from model to model, for example, European models have a comma at the end of the decimal point, inverse trigonometric functions are shown at the beginning of the Arc, etc., so you will need to know the model number of your model to determine the character table.

| CY self-test number | Model | QR
|--|--|--
| 213 | fx-83GT X | 0
| 214 | fx-85GT X | 0
| 215 | fx-82DE X | 1
| 216 | fx-85DE X | 1
| 217 | fx-991RS X | 1
| 230 | fx-82EX | 0
| 231 | fx-85EX | 0
| 232 | fx-350EX | 0
| 234 | fx-570EX | 1
| 235 | fx-991EX | 1
| 236 | fx-82CN X | 0
| 237 | fx-350CN X | 0
| 238 | fx-95CN X | 0
| 239 | fx-991CN X | 0
| 240 | fx-530AZ | 0
| 241 | fx-JP500 | 0
| 242 | fx-JP700 | 0
| 243 | fx-JP900 | 1
| 246 | fx-92 Spéciale Collège | 1
| 247 | fx-92B Spéciale Collège | 1
| 250 | fx-87DE X | 1
| 251 | fx-991DE X | 1
| 252 | fx-82SP X | 0
| 253 | fx-350SP X | 0
| 254 | fx-570SP X | 1
| 255 | fx-991SP X | 1
| 256 | fx-82AR X | 0
| 257 | fx-95AR X | 0
| 258 | fx-570AR X | 1
| 259 | fx-991AR X | 1
| 266 | fx-82SP X II | 0
| 267 | fx-350SP X II | 0
| 268 | fx-570SP X II | 1
| 269 | fx-991SP X II | 1
| 270 | fx-82LA X | 0
| 271 | fx-350LA X | 0
| 272 | fx-570LA X | 1
| 273 | fx-991LA X | 1
| 291 | fx-82CE X | 0
| 292 | fx-85CE X | 0
| 293 | fx-350CE X | 1
| 294 | fx-991CE X | 1
| 295 | fx-92+ Spéciale Collège | 1
| 296 | fx-85SP X II | 0
| 297 | fx-97SG X | 1
| 298 | fx-580VN X | 0

| EY self-test number | Model | QR
|--|--|--
| 001 | fx-82CW | 1
| 002 | fx-85CW | 1
| 003 | fx-350CW | 1
| 004 | fx-570CW | 1
| 005 | fx-991CW | 1
| 006 | fx-92 Collège | 1
| 007 | fx-92B Secondaire | 1
| 008 | fx-82SP CW | 1
| 009 | fx-85SP CW | 1
| 010 | fx-570SP CW | 1
| 011 | fx-991SP CW | 1
| 012 | fx-82DE CW | 1
| 013 | fx-85DE CW | 1
| 014 | fx-87DE CW | 1
| 015 | fx-991DE CW | 1
| 016 | fx-800DE CW | 1
| 021 | fx-82NL | 1
| 023 | fx-880BTG | 1
| 024 | fx-82CN CW | 0
| 025 | fx-95CN CW | 0
| 026 | fx-350CN CW | 0
| 027 | fx-991CN CW | 0
| 028 | fx-999CN CW | 0
| 029 | fx-JP500CW | 0
| 030 | fx-JP700CW | 0
| 031 | fx-JP900CW | 1
| 032 | fx-550AZ | 1
| 036 | fx-82LA CW | 1
| 038 | fx-570LA CW | 1
| 039 | fx-991LA CW | 1
| 040 | fx-83GT CW | 1
| 041 | fx-85GT CW | 1
| 044 | fx-8200 AU | 1
| 046 | fx-570MY CW | 1
| 047 | fx-810DE CW | 1

## U data
The calculator is written with a 12-digit serial number at the same time as the ROM is flashed at the factory.
It is assumed that the serial number stores information about the work produced by the machine, the flow lines, etc., but the more specific coding rules are not clear.

## M data
### Mode code (1~4)
In the normal calculation interface, digits 1~2 represent the main mode and digits 3~4 are the submode.
For example, if Calculate mode has no submode, it will be C100; if it's the "1-Variable" submode in Statistical Mode, it will be 0301.

The following table lists the mode codes and corresponding names with no submode.

| 06 | Matrix
| 07 | Vector
| 0D | Spreadsheet
| 0E | Algorithm
| 4F | Math Box
| 88 | Table
| 89 | Verify
| C1 | Calculate
| C4 | Complex

The following tables give the meanings of each mode code and submode code.

Base-N mode
<table>
<thead>
  <tr>
    <td rowspan="4">02</td>
    <td rowspan="4">Base-N</td>
    <td>09</td>
    <td>Dec</td>
  </tr>
  <tr>
    <td>0F</td>
    <td>Hex</td>
  </tr>
  <tr>
    <td>01</td>
    <td>Bin</td>
  </tr>
  <tr>
    <td>07</td>
    <td>Oct</td>
  </tr>
</thead>
</table>

Statistics mode. Among them, the **"linear regression"** and **"quadratic regression"** of some models use descending power order instead of ascending power order.

<table>
<thead>
  <tr>
    <td rowspan="8">03</td>
    <td rowspan="8">Statistics</td>
    <td>01</td>
    <td>Single-Variable Statistic<br>[1-Variable]</td>
  </tr>
  <tr>
    <td>02</td>
    <td>Linear Regression<br>[y = a+bx] / [y = ax+b]</td>
  </tr>
  <tr>
    <td>03</td>
    <td>Quadratic Regression<br>[y = a+bx+cx²] / [y = ax²+bx+c]</td>
  </tr>
  <tr>
    <td>04</td>
    <td>Logarithmic Regression<br>[y = a+b･ln(x)]</td>
  </tr>
  <tr>
    <td>05</td>
    <td>exponential Regression<br>[y = a･e^(bx)]</td>
  </tr>
  <tr>
    <td>06</td>
    <td>ab exponential Regression<br>[y = a･b^x]</td>
  </tr>
  <tr>
    <td>07</td>
    <td>Power Regression<br>[y = a･x^b]</td>
  </tr>
  <tr>
    <td>08</td>
    <td>Inverse Regression<br>[y = a+b/x]</td>
  </tr>
</thead>
</table>

Distribution mode
<table>
<thead>
  <tr>
    <td rowspan="7">0C</td>
    <td rowspan="7">Distribution</td>
    <td>01</td>
    <td>Normal Probability Density<br>[Normal PD]</td>
  </tr>
  <tr>
    <td>02</td>
    <td>Normal Cumulative Distribution<br>[Normal PD]</td>
  </tr>
  <tr>
    <td>03</td>
    <td>Inverse Normal Cumulative<br>Distribution [Inverse Normal]</td>
  </tr>
  <tr>
    <td>04</td>
    <td>Binomial Probability [Binomial PD]</td>
  </tr>
  <tr>
    <td>05</td>
    <td>Binomial Cumulative Distribution<br>[Binomial CD]</td>
  </tr>
  <tr>
    <td>06</td>
    <td>Poisson Probability<br>[Poisson PD]</td>
  </tr>
  <tr>
    <td>07</td>
    <td>Poisson Cumulative Distribution<br>[Poisson CD]</td>
  </tr>
</thead>
</table>

Equation mode, of which submode 08 is "Solver", which is new to CW II.
<table>
<thead>
  <tr>
    <td rowspan="7">45</td>
    <td rowspan="7">Equation</td>
    <td>01</td>
    <td rowspan="3">Simultaneous Equation</td>
    <td>2 Unknowns</td>
  </tr>
  <tr>
    <td>02</td>
    <td>3 Unknowns</td>
  </tr>
  <tr>
    <td>03</td>
    <td>4 Unknowns</td>
  </tr>
  <tr>
    <td>04</td>
    <td rowspan="3">Polynomial Equation</td>
    <td>ax²+bx+c</td>
  </tr>
  <tr>
    <td>05</td>
    <td>ax³+bx²+cx+d</td>
  </tr>
  <tr>
    <td>06</td>
    <td>ax⁴+bx³+cx²+dx+e</td>
  </tr>
  <tr>
    <td>08</td>
    <td colspan="2">Solver</td>
  </tr>
</thead>
</table>

Ratio mode
<table>
<thead>
  <tr>
    <td rowspan="2">4A</td>
    <td rowspan="2">Ratio</td>
    <td>01</td>
    <td>A:B=X:D</td>
  </tr>
  <tr>
    <td>02</td>
    <td>A:B=C:X</td>
  </tr>
</thead>
</table>

Inequality mode.
Among them, the submode code is used to refer to the degree of the polynomial equation,
and the sign of the inequality also needs to be determined using **7~8 digits** in the M data.
For example, M-**4B04**DD**01**00 means ax²+bx+c<0
<table>
<thead>
  <tr>
    <td rowspan="12">4B</td>
    <td rowspan="12">Inequality</td>
    <td rowspan="4">04</td>
    <td>00</td>
    <td>ax²+bx+c&gt;0</td>
  </tr>
  <tr>
    <td>01</td>
    <td>ax²+bx+c&lt;0</td>
  </tr>
  <tr>
    <td>02</td>
    <td>ax²+bx+c≥0</td>
  </tr>
  <tr>
    <td>03</td>
    <td>ax²+bx+c≤0</td>
  </tr>
  <tr>
    <td rowspan="4">05</td>
    <td>00</td>
    <td>ax³+bx²+cx+d&gt;0</td>
  </tr>
  <tr>
    <td>01</td>
    <td>ax³+bx²+cx+d&lt;0</td>
  </tr>
  <tr>
    <td>02</td>
    <td>ax³+bx²+cx+d≥0</td>
  </tr>
  <tr>
    <td>03</td>
    <td>ax³+bx²+cx+d≤0</td>
  </tr>
  <tr>
    <td rowspan="4">06</td>
    <td>00</td>
    <td>ax⁴+bx³+cx²+dx+e&gt;0</td>
  </tr>
  <tr>
    <td>01</td>
    <td>ax⁴+bx³+cx²+dx+e&lt;0</td>
  </tr>
  <tr>
    <td>02</td>
    <td>ax⁴+bx³+cx²+dx+e≥0</td>
  </tr>
  <tr>
    <td>03</td>
    <td>ax⁴+bx³+cx²+dx+e≤0</td>
  </tr>
</thead>
</table>

### Result area template (5~6)
Bits 5~6 of the M data are used to indicate the template style displayed in the result area.
For example, if the result of calculating Rec(r,θ) has two parts x=a, y=b, 11 is used to refer to it.

| 11 | Rec(r,θ) | x=a,y=b
| 12 | Pol(x,y) | r=a,θ=b
| 13 | Solve for ?? | ??=a, L-R=b
| 14 | ÷R | a,R=b
| 15 | →Simp | F=b,a

Among them, 13 is used in the solution interface of SOLVE. The target variable for solution needs to be referred to by digits 9~10 of the M data, which is the hexadecimal code corresponding to the character of the solution variable.
For example, if the result is x=a, L-R=b, and it is solved for x, these two digits are 48 (47 in CW II).

For other values, the default is to treat the algebraic form of the complex number a+bi.
Even if it is temporarily converted to polar coordinate form r∠θ (use →r∠θ in CW I or use the format menu conversion in CW II), it will not be exported and indicated in the M data of QR.
If the default display is set to polar coordinate format, you need to view the S data.

### Numerical display format (7~8)
The 7th digit is used to indicate the display format of the result area value, and the 8th digit indicates the format in which it is stored in the variable.
For example, if 7~8 is AD, the result needs to be displayed as a decimal, but it can be converted into a standard result display.

| 1 | Degrees minutes seconds format
| 2 | Press [ENG] 4 times
| 3 | Press [ENG] thrice
| 4 | Press [ENG] twice
| 5 | Press [ENG] once
| 6 | Press [SHIFT] [ENG] once
| 7 | Press [SHIFT] [ENG] twice
| 8 | Press [SHIFT] [ENG] thrice
| 9 | Press [SHIFT] [ENG] 4 times
| A | Decimal
| B | Improper fraction
| C | Mixed fraction
| D | Standard
| E | Recurring decimal
| F | Prime factorization

Among them, for the display of engineering notation, after pressing [ENG] for the first time, the decimal point will try to move to the right until the exponent of ×10 is a multiple of 3.
If it is already there, it will not move.
Continue to press [ENG] to continue moving right, up to 4 times.
[SHIFT][ENG] is the same, but it just moves the decimal point to the left.
CW II introduced the function of moving the decimal point with the [←][→] keys, and the principle is similar.

### Non-calculation screen
If the QR is generated in the error interface, mode menu, or setting menu, the first two digits will be the corresponding error or menu code.
At this time, accessing the link will jump to the corresponding manual page.

| X1 | Calculate
| X2 | Base-N
| X3 | Statistics
| X4 | Complex
| X5 | Equation
| X6 | Matrix
| X7 | Vector
| X8 | Table
| X9 | Verify
| XA | Ratio
| XB | Inequality
| XC | Distribution
| XD | Spreadsheet
| XE | Algorithm
| XF | Math Box
| Y1 | AC Break
| Y2 | Syntax ERROR
| Y3 | Math ERROR
| Y4 | Range ERROR
| Y5 | Cannot Simplify
| Y6 | Circular ERROR
| Y7 | Stack ERROR
| Y8 | Argument ERROR
| Y9 | Dimension ERROR
| YA | Cannot Solve
| YB | Time Out
| YC | Variable ERROR
| YE | Memory ERROR
| YG | No Operator
| YH | Not Defined
| YZ | Nesting ERROR<br>(ERREUR branch in French)
| Z0 | Setup
| Z1 | Setup (2nd level)

## S data
The length of S data is generally 28 characters, and it stores the calculator’s setting data, such as angle units, etc.

The following table shows the setting names corresponding to characters in different positions, and the setting items corresponding to different parameter values.
<table>
<thead>
  <tr>
    <th>Digit(s)</th>
    <th>Setting name</th>
    <th>Parameter value</th>
    <th>Setting items</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td rowspan="4">1</td>
    <td rowspan="4">Number Format(Main)</td>
    <td>0</td>
    <td>Norm1</td>
  </tr>
  <tr>
    <td>4</td>
    <td>Norm2</td>
  </tr>
  <tr>
    <td>8</td>
    <td>FixX</td>
  </tr>
  <tr>
    <td>9</td>
    <td>SciX</td>
  </tr>
  <tr>
    <td>2</td>
    <td>Number Format(Sub)</td>
    <td>0~9</td>
    <td>X in FixX/SciX</td>
  </tr>
  <tr>
    <td rowspan="2">3</td>
    <td rowspan="2">Decimal Mark</td>
    <td>0</td>
    <td>Comma</td>
  </tr>
  <tr>
    <td>1</td>
    <td>Dot</td>
  </tr>
  <tr>
    <td rowspan="3">4</td>
    <td rowspan="3">Angle Unit</td>
    <td>4</td>
    <td>Degree</td>
  </tr>
  <tr>
    <td>5</td>
    <td>Radian</td>
  </tr>
  <tr>
    <td>6</td>
    <td>Gradian</td>
  </tr>
  <tr>
    <td rowspan="2">5</td>
    <td rowspan="2">Input</td>
    <td>0</td>
    <td>LineI</td>
  </tr>
  <tr>
    <td>1</td>
    <td>MathI</td>
  </tr>
  <tr>
    <td rowspan="2">6</td>
    <td rowspan="2">Fraction Result</td>
    <td>0</td>
    <td>d/c</td>
  </tr>
  <tr>
    <td>1</td>
    <td>ab/c</td>
  </tr>
  <tr>
    <td rowspan="2">7</td>
    <td rowspan="2">Complex Result</td>
    <td>0</td>
    <td>a+bi</td>
  </tr>
  <tr>
    <td>1</td>
    <td>r∠θ</td>
  </tr>
  <tr>
    <td rowspan="2">8</td>
    <td rowspan="2">Statistics Frequency</td>
    <td>0</td>
    <td>Off</td>
  </tr>
  <tr>
    <td>1</td>
    <td>On</td>
  </tr>
  <tr>
    <td rowspan="2">9</td>
    <td rowspan="2">Recurring Decimal</td>
    <td>0</td>
    <td>Off</td>
  </tr>
  <tr>
    <td>1</td>
    <td>On</td>
  </tr>
  <tr>
    <td rowspan="2">10</td>
    <td rowspan="2">Simplify</td>
    <td>0</td>
    <td>Auto</td>
  </tr>
  <tr>
    <td>1</td>
    <td>Manual</td>
  </tr>
  <tr>
    <td rowspan="2">11</td>
    <td rowspan="2">Output</td>
    <td>0</td>
    <td>MathO(LineO)</td>
  </tr>
  <tr>
    <td>1</td>
    <td>DecimalO</td>
  </tr>
  <tr>
    <td rowspan="2">12</td>
    <td rowspan="2">Auto Power Off</td>
    <td>0</td>
    <td>10 Min.</td>
  </tr>
  <tr>
    <td>1</td>
    <td>60 Min.</td>
  </tr>
  <tr>
    <td rowspan="3">13</td>
    <td rowspan="3">Table Type</td>
    <td>0</td>
    <td>f(x)</td>
  </tr>
  <tr>
    <td>1</td>
    <td>f(x),g(x)</td>
  </tr>
  <tr>
    <td>2</td>
    <td>g(x)</td>
  </tr>
  <tr>
    <td rowspan="2">14</td>
    <td rowspan="2">Engineer Symbol</td>
    <td>0</td>
    <td>Off</td>
  </tr>
  <tr>
    <td>1</td>
    <td>On</td>
  </tr>
  <tr>
    <td rowspan="2">15</td>
    <td rowspan="2">Digit Seperator</td>
    <td>0</td>
    <td>Off</td>
  </tr>
  <tr>
    <td>1</td>
    <td>On</td>
  </tr>
  <tr>
    <td rowspan="2">16</td>
    <td rowspan="2">Multi-Line Font</td>
    <td>A</td>
    <td>Small Font</td>
  </tr>
  <tr>
    <td>E</td>
    <td>Normal Font</td>
  </tr>
  <tr>
    <td rowspan="2">17</td>
    <td rowspan="2">Equation Complex Root</td>
    <td>0</td>
    <td>Off</td>
  </tr>
  <tr>
    <td>1</td>
    <td>On</td>
  </tr>
  <tr>
    <td>18</td>
    <td>Language</td>
    <td>/</td>
    <td>Vary on Models</td>
  </tr>
  <tr>
    <td rowspan="2">19</td>
    <td rowspan="2">Spreadsheet: Auto Calc</td>
    <td>0</td>
    <td>Off</td>
  </tr>
  <tr>
    <td>1</td>
    <td>On</td>
  </tr>
  <tr>
    <td rowspan="2">20</td>
    <td rowspan="2">Spreadsheet: Show Cell</td>
    <td>0</td>
    <td>Formula</td>
  </tr>
  <tr>
    <td>1</td>
    <td>Value</td>
  </tr>
  <tr>
    <td rowspan="2">21</td>
    <td rowspan="2">QR Code Version</td>
    <td>B</td>
    <td>Version 11</td>
  </tr>
  <tr>
    <td>3</td>
    <td>Version 3</td>
  </tr>
  <tr>
    <td rowspan="4">22</td>
    <td rowspan="4">Algorithm: Background</td>
    <td>0</td>
    <td>Axes</td>
  </tr>
  <tr>
    <td>1</td>
    <td>Axes/Grid</td>
  </tr>
  <tr>
    <td>2</td>
    <td>Axes/xy</td>
  </tr>
  <tr>
    <td>3</td>
    <td>None</td>
  </tr>
  <tr>
    <td rowspan="2">23</td>
    <td rowspan="2">Algorithm: Unit Setting</td>
    <td>0</td>
    <td>pixels</td>
  </tr>
  <tr>
    <td>1</td>
    <td>units</td>
  </tr>
  <tr>
    <td>24</td>
    <td colspan="3">Unknown</td>
  </tr>
  <tr>
    <td>25~28</td>
    <td colspan="3">Checksum</td>
  </tr>
</tbody>
</table>

Among them, the input/output settings are jointly determined by the parameter values of digits 5 and 11.
The last four digits are suspected to be calculated through some algorithm, similar to a check code.
On some CW I models released earlier, this is used to verify authenticity.

## Internal numerical storage principle
The calculator internally uses a method similar to BCD code (Binary-Coded Decimal) to store values.
From ES to CW I series, 10 bytes (i.e. 20 digits) are used to store values.
CW II uses 14 bytes (28 digits) for storage due to improved accuracy, but there is no fundamental change in the storage methods of the two.

Some data exported from QR, such as <u>R data</u>, also export the values stored in the calculator as hexadecimal, <u>but the format has been changed</u>.

Let’s first take the 10-byte and 20-digit storage method as an example to see how it is stored inside the calculator, and then talk about the similarities and differences between it and the exported data.

---

When the value is 0, all 20 digits are 0.
If it is not 0, taking $$ 1.23456789112345\times 10^{67} $$ as an example, the value will be stored in the calculator in the following form:  
<mark style="background-color: #FFA53C">0</mark><mark style="background-color: #61D156">123456789112345</mark><mark style="background-color: #FFE828">67</mark><mark style="background-color: #9EBAFD">01</mark>  
<mark style="background-color: #FFA53C">Area ①</mark>: The first digit is the type flag, which marks the storage type format of the value;  
<mark style="background-color: #61D156">Area ②</mark>: The 2nd digit to 5th digit from the right is the significant number storage area. You can see that it occupies 15 digits, which corresponds to 15 digits of significant digits;  
<mark style="background-color: #FFE828">Area ③</mark> and <mark style="background-color: #9EBAFD">area ④</mark>: The fourth and third digits from the right and the first and second digits from the right are the sign and exponent digits. For different storage formats, the meanings of the two areas will be slightly different.

The following is an introduction to each storage format.

### Floating point format
The floating point format (<mark style="background-color: #FFA53C">area ①</mark>) flag is <mark style="background-color: #FFA53C">0</mark>.
When storing a value, the calculator intercepts 15 significant digits and writes it in scientific notation with only 1 digit in the integer part, and stores it in <mark style="background-color: #61D156">area ②</mark>.
If the exponent in scientific notation is >0, <mark style="background-color: #FFE828">area ③</mark> stores the exponent directly,
if the value itself is >0, <mark style="background-color: #9EBAFD">area ④</mark> takes <mark style="background-color: #9EBAFD">01</mark>, otherwise it takes <mark style="background-color: #9EBAFD">06</mark>.
If the exponent in scientific notation is <0, the inverse of the exponent, i.e., 100-|exponent| is used in <mark style="background-color: #FFE828">area ③</mark>.
In this case, if the value itself is >0, <mark style="background-color: #9EBAFD">area ④</mark> takes <mark style="background-color: #9EBAFD">00</mark>, otherwise it takes <mark style="background-color: #9EBAFD">05</mark>.

| | <mark style="background-color: #FFE828">Area ③</mark> | <mark style="background-color: #9EBAFD">Area ④</mark>
|--|--|--
| Exponent>0, value>0 | Exponent | 01
| Exponent>0, value<0 | Exponent | 06
| Exponent<0, value>0 | 100-exponent | 00
| Exponent<0, value<0 | 100-exponent | 05

Here are some examples to facilitate understanding
1. Writing 123456789 as $$ 1.23456789000000\times 10^{08} $$, both the number and the exponent part are >0, so it will be stored as  
<mark style="background-color: #FFA53C">0</mark><mark style="background-color: #61D156">123456789000000</mark><mark style="background-color: #FFE828">08</mark><mark style="background-color: #9EBAFD">01</mark>
2. By intercepting the first 15 significant digits of $$ 0.0000987654321123456789 $$ and write it as $$ 9.87654321123456\times ^{-05} $$, the number is >0, the exponent part is <0, and 100-5=95, then  
<mark style="background-color: #FFA53C">0</mark><mark style="background-color: #61D156">987654321123456</mark><mark style="background-color: #FFE828">95</mark><mark style="background-color: #9EBAFD">00</mark>
3. $$ −201420202022\times ^{−23} $$, will be written as $$ −2.01420202022000\times ^{−12} $$, the number is <0, the exponent part is also <0, and 100-12=88, then  
<mark style="background-color: #FFA53C">0</mark><mark style="background-color: #61D156">201420202022000</mark><mark style="background-color: #FFE828">88</mark><mark style="background-color: #9EBAFD">05</mark>

### Fraction format
The fraction format flag is <mark style="background-color: #FFA53C">2</mark>.
When storing a fraction, if the absolute value of the fraction is <1, the fraction line is represented by A and stored in <mark style="background-color: #61D156">area ②</mark> in the form of <mark style="background-color: #61D156">numerator A fraction</mark>, with 0 padding after less than 15 digits.
If the absolute value of the fraction is >1, it needs to be converted into a mixed number and then stored in the form of <mark style="background-color: #61D156">integer A numerator A fraction</mark>.
The 15th bit is also supplemented with 0. This is essentially the same as the linear input representation of the fraction.
<mark style="background-color: #FFE828">Area ③</mark> is the "length" of the fraction, as explained below with an example.
<mark style="background-color: #9EBAFD">Area ④</mark> is the symbol of the fraction, 
if it is a positive number, it will be <mark style="background-color: #9EBAFD">01</mark>, and if it is a negative number, it will be <mark style="background-color: #9EBAFD">06</mark>.

1. $$ \displaystyle\frac{2}{3} $$ is expressed as  
<mark style="background-color: #FFA53C">2</mark><mark style="background-color: #61D156"><u>2A3</u>000000000000</mark><mark style="background-color: #FFE828">03</mark><mark style="background-color: #9EBAFD">01</mark>
2. $$ \displaystyle-\frac{2}{300} $$ is expressed as  
<mark style="background-color: #FFA53C">2</mark><mark style="background-color: #61D156"><u>2A300</u>0000000000</mark><mark style="background-color: #FFE828">05</mark><mark style="background-color: #9EBAFD">06</mark>  
In the above two examples, underlines are used to mark the length of the fraction. You can see that <mark style="background-color: #61D156">area ②</mark> of both is <mark style="background-color: #61D156">2A3000000000000</mark>,
but due to the difference in <mark style="background-color: #FFE828">area ③</mark>, the values expressed are different.
3. $$ \displaystyle\frac{56789}{1234}>1 $$, converted to a mixed fraction $$ \displaystyle 46\frac{25}{1234} $$, then  
<mark style="background-color: #FFA53C">2</mark><mark style="background-color: #61D156"><u>46A25A1234</u>00000</mark><mark style="background-color: #FFE828">10</mark><mark style="background-color: #9EBAFD">01</mark>  

Under normal circumstances, the length of a fraction cannot exceed 10.
If the operation exceeds 10, it will be converted into a floating point number for storage and cannot be converted back.
This is also why some fractions that do not seem to exceed the calculation range are still expressed as decimals.
It is probably because the length of the number is expressed as an improper fraction and is not greater than 10, but the length is expressed as a mixed fraction.
The degree is over 10. If the value of the corresponding variable in an emulator is modified through an exception or using a memory editor, it can still be displayed.

### Degrees minutes seconds format
The degrees minutes seconds format flag is <mark style="background-color: #FFA53C">4</mark>.

At this time, it uses the same storage method as floating point numbers, except that the calculator converts it into degrees, minutes and seconds format by default when displaying.

### Radical format
The radical format flag is <mark style="background-color: #FFA53C">8</mark>.
The calculator can display the addition of up to two different radical expressions, which can be called radical expression 1 and radical expression 2.
Each radical expression can have up to 3 digits.
The coefficient of the radical expression is a fraction.
The numerator can have up to 2 digits.
The two fractions can be different, and the two fractions will be displayed when displayed.

For example, there is radical 1: $$ \displaystyle\sqrt{aaa}\times\frac{bb}{cc} $$ and radical 2: $$ \displaystyle\sqrt{ddd}\times\frac{ee}{ff} $$, they will be stored in <mark style="background-color: #61D156">area ②</mark> as  
<mark style="background-color: #61D156">aaa bb cc 0 ddd ee ff</mark>  
It occupies exactly 15 bits, and the 0 in the middle can be understood as a separator.

At this time, <mark style="background-color: #FFE828">area ③</mark> is used to indicate the positive and negative sign of radical formula 2, and <mark style="background-color: #9EBAFD">area ④</mark> is used to indicate the positive and negative sign of radical formula 1.
Similarly, the positive number is 01, and the negative number is 06.

1. $$ \displaystyle -\frac{12\sqrt{345}}{67} + \frac{78\sqrt{777}}{89} $$ will be stored as  
<mark style="background-color: #FFA53C">8</mark><mark style="background-color: #61D156"><u>345</u>12<u>67</u>0<u>777</u>78<u>89</u></mark><mark style="background-color: #FFE828">01</mark><mark style="background-color: #9EBAFD">06</mark>
2. $$ \displaystyle\sqrt{3} $$ can be written as $$ \displaystyle\sqrt{000}\times\frac{00}{01}+\sqrt{003}\times\frac{01}{01} $$, then  
<mark style="background-color: #FFA53C">8</mark><mark style="background-color: #61D156"><u>000</u>00<u>01</u>0<u>003</u>01<u>01</u></mark><mark style="background-color: #FFE828">00</mark><mark style="background-color: #9EBAFD">01</mark>
3. $$ \displaystyle 2-\frac{\sqrt{7}}{2} $$ can be written as $$ \displaystyle \sqrt{001}\times\frac{02}{01}-\sqrt{007}\times\frac{01}{02} $$, then  
<mark style="background-color: #FFA53C">8</mark><mark style="background-color: #61D156"><u>001</u>02<u>01</u>0<u>007</u>01<u>02</u></mark><mark style="background-color: #FFE828">06</mark><mark style="background-color: #9EBAFD">01</mark>

For example 1 above, when the calculator displays the result, it will be divided into $$ \displaystyle\frac{5226\sqrt{777}-1068\sqrt{345}}{5963} $$ for display, but if this formula is entered into the input area for calculation,
it will exceed the limit of the radical format, and the result will be converted and stored as floating point format.

### ERROR value
The error value flag is <mark style="background-color: #FFA53C">F</mark>.
Normally no ERROR value is stored in the variable, to create an ERROR value, there is the TABLE method in ES PLUS, and in CW I, you can type the following equation, and then press [CALC][=][=], M will light up, [AC].
Check the value of M is empty. Recall M directly to get a math error. Looking at the memory, the memory area where M is located begins with F3, and in the analysis of the M data described above, Y3 corresponds to a math error.

![2 Casio CW I calculator screen captures. One is showing the equation M:M=Integral(1, ×10^85-*10^99, 9×10^99), the other shows the variable menu with an empty value in M.](/casiocalc/images/classwiz_qr_5.png)
	
If you enter $$ =\div $$ in Table mode, a cell with syntax error will be created, and after exporting with QR, the value stored in that cell (how to check it will be explained later) starts with F2, which happens to be the syntax error corresponding to Y2 in the M data.

In other words, although all values starting with F are ERROR values, they are also reserved.

### Others
In fact, the internal storage will also have data with flag digit 6, which is equivalent to a pointer to a matrix or vector.
Since QR directly derives the values stored in the matrix, it will not be described here.

### Numerical representation of CW II
CW II increases the precision to 23 digits and uses 14 bytes and 28 digits to store floating point numbers, but the representation method does not fundamentally change.
The last 4 digits are also used to store <mark style="background-color: #FFE828">area ③</mark> and <mark style="background-color: #9EBAFD">area ④</mark>.
The floating point number format needs to occupy the middle 23-bit <mark style="background-color: #61D156">area ②</mark>, and the remaining numerical formats are padded with 0s.

1. $$ \displaystyle 9.0000000000000000075528 $$ is represented as  
<mark style="background-color: #FFA53C">0</mark><mark style="background-color: #61D156">90000000000000000075528</mark><mark style="background-color: #FFE828">00</mark><mark style="background-color: #9EBAFD">01</mark>
2. $$ \displaystyle \frac{570}{991} $$ as  
<mark style="background-color: #FFA53C">0</mark><mark style="background-color: #61D156"><u>570A991</u>0000000000000000</mark><mark style="background-color: #FFE828">07</mark><mark style="background-color: #9EBAFD">01</mark>  
The length of the fraction still cannot exceed 10
3. $$ \displaystyle -\frac{12\sqrt{345}}{67}+\frac{78\sqrt{777}}{89} $$ as  
<mark style="background-color: #FFA53C">8</mark><mark style="background-color: #61D156"><u>345</u>12<u>67</u>0<u>777</u>78<u>89</u>00000000</mark><mark style="background-color: #FFE828">01</mark><mark style="background-color: #9EBAFD">06</mark>  
8 zeroes are padded in <mark style="background-color: #61D156">area ②</mark>

## R data
Now that the storage principle of internal values has been introduced, let’s take a look at how the QR function exports these values.

Different from the internal storage method, the R data exported by QR will swap the positions of <mark style="background-color: #FFE828">area ③</mark> and <mark style="background-color: #9EBAFD">area ④</mark>.

### Calculate, Complex modes
When R data is exported on the screen of ordinary calculation results, it needs to be divided into two parts, each of which has 20 digits, which corresponds to the 20 digits of the internal storage method.

If the calculation result is relatively "simple", such as the result of a numerical operation, the second part of the R data is all 0, and only the first part (the first 20 digits) is useful, and can be parsed according to the storage method described above (the positions of area ③ and area ④ need to be swapped).

If the calculation result is relatively "complicated" and the calculator uses two parts for display, such as the Pol and Rec coordinate operations, the ÷R remainder operation, the result of an imaginary number in Complex mode, etc., it is necessary to parse the two parts of the R data and determine the method for displaying the two values according to the corresponding parameters in the M data.

1. After exporting the calculation result $$ \displaystyle 1.23456789112345\times 10^{67} $$, the R data will be  
<mark style="background-color: #FFA53C">0</mark><mark style="background-color: #61D156">123456789112345</mark><mark style="background-color: #9EBAFD">01</mark><mark style="background-color: #FFE828">67</mark>00000000000000000000  
Please note that the positions of <mark style="background-color: #FFE828">area ③</mark> and <mark style="background-color: #9EBAFD">area ④</mark> have been reversed.
2. The result of calculating $$ \displaystyle \textrm{Pol(1, −1)} $$ is $$ \displaystyle r = \sqrt{2},\theta = −45 $$, which is derived as  
<mark style="background-color: #FFA53C">8</mark><mark style="background-color: #61D156"><u>000</u>00<u>01</u>0<u>002</u>01<u>01</u></mark><mark style="background-color: #9EBAFD">00</mark><mark style="background-color: #FFE828">01</mark><mark style="background-color: #FFA53C">0</mark><mark style="background-color: #61D156">450000000000000</mark><mark style="background-color: #9EBAFD">06</mark><mark style="background-color: #FFE828">01</mark>  
3. $$ \displaystyle \frac{5}{7} - \sqrt{2} + \sqrt{3}i + 2i $$, at this time the first part is the real part and the last part is the imaginary part  
<mark style="background-color: #FFA53C">8</mark><mark style="background-color: #61D156"><u>001</u>05<u>07</u>0<u>002</u>01<u>01</u></mark><mark style="background-color: #9EBAFD">01</mark><mark style="background-color: #FFE828">06</mark><mark style="background-color: #FFA53C">8</mark><mark style="background-color: #61D156"><u>001</u>02<u>01</u>0<u>003</u>01<u>01</u></mark><mark style="background-color: #9EBAFD">01</mark><mark style="background-color: #FFE828">01</mark>

In addition to ordinary calculations, when calculations involving equations, inequalities, matrices, vectors, and other calculations contain multiple results, there will be a storage flag for this data at the beginning of the R data, followed by numerical value parts, each of which is also 20 bits, and the results corresponding to the numerical values of each part will be decided based on the M data and the storage flag at the beginning of the R data.



\<WIP\>
