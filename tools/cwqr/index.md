---
title: ClassWiz QR parser
layout: default
parent: Tools
---

Welcome to the ClassWiz QR code URL parser! This is a GUI frontend for [happy sheep's QR parser](https://cwqr.pages.dev).  
The version used here is a modified fork of the original. You can visit [this page](/casiocalc/tools/cwqr/src/) if you want to see the full raw output of the parser.

NOTE: Everything is currently not fully supported.

<script defer src="src/cwqr.js"></script>
<script src="https://i.upmath.me/latex.js"></script>

<input id="qrUrl" style="width: 99%" placeholder="QR code URL" />
<button id="parse" class="btn" onclick="main()">Parse</button>

<form id="langsel">
<legend>Name language:</legend>
<input type="radio" id="lang_en" name="lang" value="Global" checked /><label for="lang_en">English</label>
<input type="radio" id="lang_zh" name="lang" value="CN" /><label for="lang_zh">中文</label>
<input type="radio" id="lang_vi" name="lang" value="VN" /><label for="lang_vi">Tiếng Việt</label>
</form>

<p id="report"></p>
<div id="result_" style="display: none;">
<h2>Model information</h2>
<ul><li>Model: <span id="model_name"></span> (version <span id="model_type"></span>-<span id="model_id"></span>, revision <span id="model_version"></span>)</li>
<li>Has QR code capabilities: <span id="model_qr"></span></li>
<li>Serial number: <span id="model_serialNumber"></span></li></ul>
<h2>Operating environment</h2>
Mode: <span id="mode"></span><br>
Settings:
<table><thead><tr><th>Name</th><th>Value</th></tr></thead><tbody id="setup"></tbody></table>
<div id="calculation" style="display: none;">
<h2>Calculate/Complex</h2>
<ul><li>Expression:<br><span id="expression"></span></li>
<li>Result:<br><span id="result_templated"></span><ul>
<li>Part 1: decimal <span id="result_part1_decimal"></span><br><span id="result_part1"></span></li>
<li>Part 2: decimal <span id="result_part2_decimal"></span><br><span id="result_part2"></span></li></ul>
</li></ul>
</div>
<div id="table" style="display: none;">
<h2>Table</h2>
<ul><li>f(x) (+ g(x)):<br><span id="expression_table"></span></li>
<li>Table range:<ul>
<li>Start: decimal <span id="tableRange_start_decimal"></span><br><span id="tableRange_start"></span></li>
<li>End: decimal <span id="tableRange_end_decimal"></span><br><span id="tableRange_end"></span></li>
<li>Step: decimal <span id="tableRange_step_decimal"></span><br><span id="tableRange_step"></span></li></ul>
</li></ul>
</div>
<div id="equation" style="display: none;">
<h2>Equation</h2>
<ul><li>Equation:<br><span id="equat"></span></li>
<li><div id="equation_noerror" style="display: none;">Result:<br><span id="equation_result_templated"></span><ul>
<li>Part 1: decimal <span id="equation_result_part1_decimal"></span><br><span id="equation_result_part1"></span></li>
<li>Part 2: decimal <span id="equation_result_part2_decimal"></span><br><span id="equation_result_part2"></span></li></ul>
</div>
<div id="equation_error" style="display: none;">Result: <span id="equation_result_error"></span></div>
</li></ul>
</div>
<div id="matrix" style="display: none;">
<h2>Matrix</h2>
<ul>
<li>Expression:<br><span id="expression_matrix"></span></li>
<li>MatAns:<br><span id="matans"></span></li>
<div id="MatA_" style="display: none;"><li>MatA:<br><span id="MatA"></span></li></div>
<div id="MatB_" style="display: none;"><li>MatB:<br><span id="MatB"></span></li></div>
<div id="MatC_" style="display: none;"><li>MatC:<br><span id="MatC"></span></li></div>
<div id="MatD_" style="display: none;"><li>MatD:<br><span id="MatD"></span></li></div>
</ul>
</div>
<div id="vector" style="display: none;">
<h2>Vector</h2>
<ul>
<li>Expression:<br><span id="expression_vector"></span></li>
<li>VctAns:<br><span id="vctans"></span></li>
<div id="VctA_" style="display: none;"><li>VctA:<br><span id="VctA"></span></li></div>
<div id="VctB_" style="display: none;"><li>VctB:<br><span id="VctB"></span></li></div>
<div id="VctC_" style="display: none;"><li>VctC:<br><span id="VctC"></span></li></div>
<div id="VctD_" style="display: none;"><li>VctD:<br><span id="VctD"></span></li></div>
</ul>
</div>
</div>

<script>
let result;

function getRegion() {
	let data = new FormData(document.getElementById('langsel'));
	let region;
	for (var [k, v] of data.entries()) return v;
}

function parseName(l) {
	let globalname = '';
	for (var i of l) {
		if (i.region == 'Global') globalname = i.name;
		if (i.region == getRegion()) return i.name;
	}
	return globalname;
}

function setLatexImage(id, s) {
	let a = document.getElementById(id);
	a.innerHTML = `$ ${s} $`;
	MathJax.Hub.Typeset(a);
}

function main() {
	document.getElementById('parse').disabled = true;
	document.getElementById('result_').style = 'display: none;';
	document.getElementById('calculation').style = 'display: none;';
	document.getElementById('table').style = 'display: none;';
	document.getElementById('equation').style = 'display: none;';
	document.getElementById('equation_noerror').style = 'display: none;';
	document.getElementById('equation_error').style = 'display: none;';

	try {
		result = cwqr.parseUrl(qrUrl.value);

		document.getElementById('model_name').innerHTML = result.model.name;
		document.getElementById('model_type').innerHTML = result.model.type;
		document.getElementById('model_id').innerHTML = result.model.id;
		document.getElementById('model_version').innerHTML = result.model.version;
		document.getElementById('model_qr').innerHTML = result.model.qr ? 'Yes' : 'No';
		document.getElementById('model_serialNumber').innerHTML = result.model.serialNumber;

		let mode_str;
		if (Array.isArray(result.mode)) mode_str = result.mode.length > 0 ? parseName(result.mode) : 'None';
		else {
			mode_str = parseName(result.mode.mainName);
			if (result.mode.subName.length > 0) mode_str += ` - ${parseName(result.mode.subName)}`;
		}
		document.getElementById('mode').innerHTML = mode_str;
		var tbdy = document.getElementById('setup');
		tbdy.replaceChildren();
		for (var setting of result.setup) {
			if (setting.value.length > 0) {
				var tr = document.createElement('tr');
				var td1 = document.createElement('td');
				td1.appendChild(document.createTextNode(parseName(setting.name)));
				var td2 = document.createElement('td');
				td2.appendChild(document.createTextNode(parseName(setting.value)));
				tr.appendChild(td1);
				tr.appendChild(td2);
				tbdy.appendChild(tr);
			}
		}

		if (result.expression != undefined) {
			if (result.result != undefined) {
				if (result.matrix != undefined) {
					setLatexImage('expression_matrix', result.expression);
					setLatexImage('matans', result.result[0].latex);
					for (var i of result.matrix) {
						setLatexImage(i.name, i.latex);
						document.getElementById(`${i.name}_`).style = '';
					}

					document.getElementById('matrix').style = '';
				} else if (result.vector != undefined) {
					setLatexImage('expression_vector', result.expression);
					setLatexImage('vctans', result.result[0].latex);
					for (var i of result.vector) {
						setLatexImage(i.name, i.latex);
						document.getElementById(`${i.name}_`).style = '';
					}

					document.getElementById('vector').style = '';
				} else {
					setLatexImage('expression', result.expression);
					setLatexImage('result_templated', result.result[0].latex);
					setLatexImage('result_part1', result.result[1].latex);
					setLatexImage('result_part2', result.result[2].latex);
					document.getElementById('result_part1_decimal').innerHTML = result.result[1].decimal;
					document.getElementById('result_part2_decimal').innerHTML = result.result[2].decimal;

					document.getElementById('calculation').style = '';
				}

			} else if (result.tableRange != undefined) {
				setLatexImage('expression_table', result.expression);
				setLatexImage('tableRange_start', result.tableRange[0].latex);
				setLatexImage('tableRange_end', result.tableRange[1].latex);
				setLatexImage('tableRange_step', result.tableRange[2].latex);
				document.getElementById('tableRange_start_decimal').innerHTML = result.tableRange[0].decimal;
				document.getElementById('tableRange_end_decimal').innerHTML = result.tableRange[1].decimal;
				document.getElementById('tableRange_step_decimal').innerHTML = result.tableRange[2].decimal;

				document.getElementById('table').style = '';
			}
		} else if (result.equation != undefined) {
			setLatexImage('equat', result.equation.latex);
			if (Array.isArray(result.result)) {
				setLatexImage('result_templated', result.result[0].latex);
				setLatexImage('result_part1', result.result[1].latex);
				setLatexImage('result_part2', result.result[2].latex);
				document.getElementById('result_part1_decimal').innerHTML = result.result[1].decimal;
				document.getElementById('result_part2_decimal').innerHTML = result.result[2].decimal;

				document.getElementById('equation_noerror').style = '';
			} else {
				document.getElementById('equation_result_error').innerHTML = parseName(result.result.name);
				document.getElementById('equation_error').style = '';
			}

			document.getElementById('equation').style = '';
		}

		document.getElementById('report').innerHTML = 'Your results are below!';
		document.getElementById('result_').style = '';
		document.getElementById('parse').disabled = false;

	} catch(err) {
		console.error(err);
		document.getElementById('report').innerHTML = `Error:<br>${err.message}`;
		document.getElementById('parse').disabled = false;
		return;
	}

}
