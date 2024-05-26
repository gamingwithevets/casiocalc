---
title: ClassWiz QR parser
layout: default
parent: Tools
---

Welcome to the ClassWiz QR code URL parser! This is a GUI frontend for [happy sheep's QR parser](https://cwqr.pages.dev), the version used here is a fork of the original.

This is currently **very experimental**.

<script defer src="src/cwqr.js"></script>

<input id="qrUrl" style="width: 99%" placeholder="QR code URL" />
<button id="parse" class="btn" onclick="main()">Parse</button>

<form id="langsel">
<legend>Name language:</legend>
<input type="radio" id="lang_en" name="lang" value="Global" checked /><label for="lang_en">English</label>
<input type="radio" id="lang_zh" name="lang" value="CN" /><label for="lang_zh">中文</label>
<!-- <input type="radio" id="lang_vi" name="lang" value="VN" /><label for="lang_zh">Tiếng Việt</label> -->
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
</div>

<script>
let result;

function getRegion() {
	let data = new FormData(document.getElementById('langsel'));
	let region;
	for (var [k, v] of data.entries()) return v;
}

function parseName(l) {
	for (var i of l) {
		if (i.region == getRegion()) return i.name;
	}
}

function main() {
	document.getElementById('parse').disabled = true;

	try {
		result = cwqr.parseUrl(qrUrl.value);
		
		/* model */
		document.getElementById('model_name').innerHTML = result.model.name;
		document.getElementById('model_type').innerHTML = result.model.type;
		document.getElementById('model_id').innerHTML = result.model.id;
		document.getElementById('model_version').innerHTML = result.model.version;
		document.getElementById('model_qr').innerHTML = result.model.qr ? 'Yes' : 'No';
		document.getElementById('model_serialNumber').innerHTML = result.model.serialNumber;

		/* mode + setup */
		let mode_str;
		if (Array.isArray(result.mode)) mode_str = result.mode.length > 0 ? result.mode[0].name[getRegion()] : 'None';
		else {
			mode_str = parseName(result.mode.mainName);
			if (result.mode.subName.length > 0) mode_str += ` - ${result.mode.subName[0].name[getRegion()]}`;
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

		document.getElementById('report').innerHTML = 'Your results are below!';
		document.getElementById('result_').style = '';
		document.getElementById('parse').disabled = false;

	} catch(err) {
		document.getElementById('report').innerHTML = `Error:<br>${err.message}`;
		document.getElementById('parse').disabled = false;
		return;
	}

}
