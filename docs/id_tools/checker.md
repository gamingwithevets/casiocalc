---
title: Checker
layout: default
parent: ID tools
---

# Checker
Actually, the checker doesn't work yet. My JavaScript skills are... questionable at best.

Please input your calculator ID below.

<input id="calcid" placeholder="ID">
<button class="btn" onclick="check()">Check</button>

<p id="result" />
<script>

function check() {
	document.getElementById("result").innerHTML = "Hang on, we're calling Casio...";
	
	let jsondata;
	fetch("https://wes.casio.com/calc/cw/api/licensecheck.php", {
		method: "POST",
		headers: {
			"Content-Type": "application/x-www-form-urlencoded",
			"sec-fetch-mode": "no-cors",
		},
		body: "calculatorid=" + encodeURIComponent(document.getElementById("calcid").value),
	})
		.then((response) => response.json())
		.then((json) => {
			jsondata = json;
		})
		.catch((error) => {
			document.getElementById("result").innerHTML = "ERROR: " + error;
		});

	var result;
	if (jsondata["license"]) {
		result = "NO F**KING WAY YOU GOT A LICENSE CODE!!! It's <b>" + json["license_code"] + "</b>, make sure to put it to good use!";
	}
	
	document.getElementById("result").innerHTML = result;
}
</script>