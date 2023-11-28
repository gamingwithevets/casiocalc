---
title: ClassWiz QR format
layout: default
---

Original document by happy sheep. Translated by Steveyboi with Google Translate + DeepL Translate

# ClassWiz QR Functional Analysis and Internal Data Storage Principles
Casio launched the ClassWiz series of calculators in 2014, which for the first time implemented a QR code (two-dimensional code) display on a function machine.
Scanning the two-dimensional code jumps to Casio's WES web site, where you can graph function images, histograms, and other charts using exported data, realizing the innovation of a function machine + a handheld machine = a graphing machine.

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
| R | Result | The result area data generally consists of two parts, each part is 20 in length
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
| 029 | fx-JP500CW | 1
| 030 | fx-JP700CW | 1
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

\<WIP\>
