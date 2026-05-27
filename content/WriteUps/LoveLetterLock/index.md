---
title: "THM: LoveLetterLock Writeup"
date: 2026-03-01
tags: ["THM", "Write Up","IDOR"]
---

{{< figure src="featured.jpg" >}}

## Background Knowledge

This writeup is for `LoveLetterLock`, a room in TryHackMe (THM) for Valentine's 2026 Event. This room focuses on Insecure Direct Object References (IDOR) Vulnerability.

Insecure Direct Object References (IDOR) occurs when an attacker is able to access unauthorized data by manipulating its identifiers. This usually happens due to weak access control implementations, where systems fails to verify if a user actually has the proper authorization towards that specific data.

More information on IDOR can be found through the OWASP official website https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html.

---

## Walkthrough

{{< figure src="1.png" >}}
Initially, I ran an Nmap scan, but it didn't reveal anything important. So, I decided to head straight to the website to look for clues. The homepage prompts you to create an account, so I registered and logged in.

<br>

{{< figure src="2.png" >}}
Next, I wanted to test the application's core functionality, so I created 2 trial letters. I noticed there were a total of four cards sitting in Cupid's archive(meaning there are 2 other letters that are not mine). Additionally, a hint on the page mentioned that each love letter receives a unique number, which served as a great lead.

<br>

{{< figure src="3.png" >}}
I then opened one of my generated letters to observe how the application handles data retrieval. By inspecting the URL structure, it became clear that the system calls the object using the format letter/(number).

<br>

{{< figure src="4.png" >}}
Exploiting this predictable structure, I simply modified the number in the letter/(number) URL path. This successfully bypassed the access controls, allowing me to view another user's letter and retrieve the final flag.
