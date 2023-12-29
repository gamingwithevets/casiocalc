---
title: Calculator ID generator
layout: default
parent: Tools
---

Welcome to the calculator ID generator! Type the first 20 characters of your desired calculator ID (no dashes, case sensitive), and we'll do some funky calculation to get the last 4 characters.

After that, you can do various things with it [here](https://wes.casio.com/calc/cw).

This tool does **NOT** check the validity of the ID. However we can give you some tips to get a valid calculator ID:
- The ID must contain alphanumeric characters only.
- The first 3 characters must be a valid version number of a still-supported calculator.
- The ID must not include uppercase O.
- The calculated last 4 characters **must be uppercase**.

<input id="calcid" placeholder="First 20 characters of ID">
<button id="gen" class="btn" onclick="main()">Generate</button>

<p id="result" />
<script>

var id;

function rotation_16bit_l(param_1, param_2) {
	let param_1_2 = param_1;
	if (param_2 != 0x10) {
		param_1 = (param_1 << (0x10 - param_2)) & 0xffff;
	}
	if (param_2 != 0) {
		param_1_2 = (param_1_2 >> param_2) & 0xffff;
	}
	return (param_1 | param_1_2) & 0xffff;
}

function rotation_16bit_r(param_1, param_2) {
	let param_1_2 = param_1;
	if (param_2 != 0x10) {
		param_1 = (param_1 >> (0x10 - param_2)) & 0xffff;
	}
	if (param_2 != 0) {
		param_1_2 = (param_1_2 << param_2) & 0xffff;
	}
	return (param_1 | param_1_2) & 0xffff;
}

function calc_id(id_f20) {
    let er4 = 0xbeef;
    let er4_old = null;

    for (let i = 0; i < 20; i += 2) {
        er4_old = er4;
        let word = (id_f20[i] | (id_f20[i + 1] << 8)) & 0xffff;
        let rotation_p1 = (word ^ er4) & 0xffff;
        let rotation_l = rotation_16bit_l(rotation_p1, 3);
        let rotation_r = rotation_16bit_r(rotation_p1, 7);
        let rotation_xor = (rotation_l ^ rotation_r ^ er4) & 0xffff;
        er4 = (rotation_xor + rotation_p1) & 0xffff;

        console.log(
            `Word @ index ${i}: ${word.toString(16).toUpperCase()}\n` +
            `XORed w/ ${er4_old.toString(16).toUpperCase()}: ${rotation_p1.toString(16).toUpperCase()}\n` +
            `Left rotation: ${rotation_l.toString(16).toUpperCase()}\n` +
            `Right rotation: ${rotation_r.toString(16).toUpperCase()}\n` +
            `XORed rotations: ${rotation_xor.toString(16).toUpperCase()}\n` +
            `Added with XOR: ${er4.toString(16).toUpperCase()}\n`
        );
    }

    return er4;
}

function write(string) {
	document.getElementById("result").innerHTML = string;
	document.getElementById("gen").disabled = false;
}

function main() {
	document.getElementById("gen").disabled = true;
	
	let id_f20_raw = document.getElementById("calcid").value;
	if (id_f20_raw.length == 1) {
		write(`You typed in 1 character. Like, seriously, what.`);
		return;
	} else if (id_f20_raw.length < 20) {
		write(`Whoops! You only typed in ${id_f20_raw.length} characters. I need ${20 - id_f20_raw.length} more!`);
		return;
	} else if (id_f20_raw.length > 20) {
		write(`Whoops! You typed in ${id_f20_raw.length} characters. That's way too much, I need ${id_f20_raw.length - 20} less!`);
		return;
	}
	
	let id_f20 = new TextEncoder().encode(id_f20_raw);
	id = id_f20_raw + calc_id(id_f20).toString(16).toUpperCase();
	
	try {
		write(`<details><summary>Your ID:</summary><pre>${id}</pre><button class="btn" onclick="copy()">Copy</button> If not working you can select and copy the ID that way.</details>`);
	}
	catch(err) {
		write(`Crap! Something screwed up somewhere.<br>${err.message}`);
	}
}

function copy() {
	navigator.clipboard.writeText(id);
	alert("Copied!");
}

</script>