---
title: "THM: Cupids Matchmaker Writeup"
date: 2026-02-27
tags: ["THM", "Write Up","XSS"]
---

{{< figure src="featured.jpg" >}}

## Background Knowledge

This writeup is for `Cupids Matchmaker`, a room in TryHackMe (THM) for Valentine's 2026 Event. This room focuses on XSS Vulnerability.

Cross-Site Scripting (XSS) is a condition when a web application accepts untrusted data from a user and sends it to a web browser without proper sanitazion. This allows an attacker to inject and execute malicious scripts (usually JavaScript) within the victim's website. There are 3 main types of XXS. They are Stored, Reflected, and Blind XSS.

<b>XSS Types:</b>
<br><b>Reflected</b> – The script is included in a request and is immediately reflected back in the response.
<br><b>Stored</b> – The script is stored on the server and executes every time a user accesses the page.
<br><b>DOM</b> – The script is executed as a result of modifying the Document Object Model(DOM) environment. More towards the client-side code.

More information on XSS can be found through the OWASP official website https://owasp.org/www-community/attacks/xss/ .

---

## Walkthrough

{{< figure src="1.png" >}}
First things first, reccoinassance. I ran an nmap scan on the machine, but it didn't reveal anything valuable. From there, I moved on to browsing the website. One detail really stood out on their landing page, the site mentioned that every match is carefully curated by real people. The fact they emphasized manual admin reviews gave me a strong lead on a potential attack vector.

<br>

{{< figure src="2.png" >}}
To confirm my suspicions, I performed a directory search and found an admin login page.

<br>

{{< figure src="3.png" >}}
I tried some basic checks on the admin portal. Simple password brute-forcing didn't work, and there were no obvious hints provided. This solidified my attack vector, to use an XSS attack, specifically Blind XSS, to steal the admin's session cookie.

<br>

{{< figure src="4.png" >}}
Since I didn't know exactly which input field was vulnerable to unsanitized data, I tested multiple payloads across all available inputs. Eventually, one of them(the one i screenshotted) successfully triggered a callback.

<br>
{{< figure src="5.png" >}}
After setting up my listener, I waited for the admin bot to review the survey. Once the bot opened it, I received a callback containing the admin's cookie, which served as the flag.