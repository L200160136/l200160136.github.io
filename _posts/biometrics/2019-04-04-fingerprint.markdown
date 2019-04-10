---
layout: post
title:  "Fingerprint"
date:   2019-04-03 16:41:26 +0700
categories: biometrics
permalink: "/:title"
---

<h2>Fingerprint Recognition</h2>
<p>Fingerprint recognition refers to the automated method of identifying or confirming the identity of an individual based on the comparisson of two fingerprints. Fingerprint recognition is one of the most well known biometrics, and it is by far the most used biometric solution for authentication on computerized systems.</p>

<h3>Alat yang diperlukan:</h3>
<hr>

<ul>
    <li>Python 3.7</li>
    <li>PIL library (pip install Pillow)</li>
    <li>Fingerprint Module : https://github.com/rtshadow/biometrics</li>
    <li>Fingerprint image</li>
</ul>

<table border="1" width="40%">
	<tr width="50px" height="50px">
		<td><center>Original Image</center></td>
		<td><center>Gabor Filter</center></td>
		<td><center>Thinning Filter</center></td>
		<td><center>Extract Minutiae</center></td>
	</tr>
	<tr width="40%" height="60%">
		<td><center><img src="/assets/biometrics/fingerprint/fingerprint.jpg" width="250px"></center></td>
		<td><center><img src="/assets/biometrics/fingerprint/fingerprint_enhanced.gif" width="250px"></center></td>
		<td><center><img src="/assets/biometrics/fingerprint/fingerprint_enhanced_thinned.gif" width="250px"></center></td>
		<td><center><img src="/assets/biometrics/fingerprint/fingerprint_enhanced_thinned_minutiae.gif" width="250px"></center></td>
	</tr>
</table>