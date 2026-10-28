# CyberHeroes — TryHackMe (Easy)

**Date:** 2026-10-28 · **Platform:** TryHackMe · **Target OS:** Linux (Ubuntu)
**Skills practiced:** Port scanning and vulnerability scripts with Nmap, web enumeration, source code review, client-side authentication weaknesses

## 1\. Summary

The target hosted a web application with a login form on port 80. The authentication logic was implemented entirely in client-side JavaScript, with the credentials hardcoded in the page source. By reviewing the source code I recovered the valid username and password and logged in successfully, obtaining the flag.

## 2\. Reconnaissance

```bash
nmap -sC -sV --script vuln <TARGET\\\_IP>
```

Scan results (998 other ports closed):

|Port|State|Service|Version|
|-|-|-|-|
|22/tcp|open|ssh|OpenSSH 8.2p1 Ubuntu 4ubuntu0.4|
|80/tcp|open|http|Apache httpd 2.4.48 (Ubuntu)|

Notable output from the NSE `vuln` and default scripts:

* `http-enum` identified `/login.html` as a possible admin folder, which became the entry point of the test.
* No DOM-based XSS, stored XSS, CSRF or file-upload issues were detected automatically.
* OS fingerprint: Linux.

SSH was not tested because no credentials or other leads for it were available in this exercise. The web server was the only realistic attack surface.

## 3\. Enumeration

Browsing to `http://<10.65.186.17>` and to `/login.html` (found by Nmap) showed a page with a login form. With no further information available, I inspected the page source (`Ctrl+U`).

The JavaScript validated the credentials directly in the browser:

* The username was compared against a hardcoded string.
* The password was stored reversed and rebuilt with a helper function (`reverse string`).
* The comparison used the bitwise `\\\&` operator instead of the logical `\\\&\\\&`. It happened to work, but it shows sloppy coding.









## 4\. Exploitation (initial access)

1. Read the username from the source.
2. Reversed the stored string to obtain the real password (my first attempt failed because \[add your reason, e.g., I typed the string without reversing it correctly]).
3. Logged in with both values and obtained the flag.

## 5\. Findings and severity

|Finding|Severity (CVSS)|Impact|
|-|-|-|
|Client-side authentication with hardcoded credentials|High|Anyone can read the credentials by viewing the page source and gain unauthorized access|
|Weak obfuscation (reversed string)|Informational|Reversing a string offers no real protection|
|Outdated Apache version (2.4.48)|Low–Medium|Older release; should be checked against known CVEs and updated|

## 6\. Remediation recommendations

* Keep Apache and the OS patched and update to a supported release.
* Perform authentication on the server side, never in client JavaScript.
* Never store credentials or secrets in HTML, JavaScript or comments.
* Store passwords as salted hashes (e.g., bcrypt or Argon2) and compare them on the server.
* Add rate limiting and account lockout to the login to prevent brute force.
* Review code with automated and manual checks before release.

## 7\. Lessons learned

* Always inspect the page source and developer tools when testing a web login.
* Obfuscation is not security.

## Tools used

Nmap , web browser .

