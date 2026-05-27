---
title: "THM: The Great Disappearing Act Writeup"
date: 2026-03-03
tags: ["THM", "Write Up", "OSINT", "API", "Docker"]
---

{{< figure src="featured.png" >}}

## Background Knowledge

This writeup is for `The Great Disappearing Act`, a room in TryHackMe (THM) for Advent of Cyber (AoC) 2025 Side Quest's Event. This room focuses on Open-Source Intelligence (OSINT), API Exploitation, and Docker Privilege Escalation.

Open-Source Intelligence (OSINT) involves gathering, analyzing, and making decisions based on data accessible in the public domain.
More information on OSINT resources can be found through the OSINT Framework website https://osintframework.com/.

API Exploitation, specifically Authorization Bypass, occurs when an application fails to properly verify if a user has the correct privileges to perform certain actions or access specific endpoints. By manipulating API requests, such as altering parameters or endpoint paths, an attacker can bypass access controls to view sensitive data or perform administrative actions.
More information on API vulnerabilities can be found through the OWASP API Security Top 10 website https://owasp.org/API-Security/editions/2023/en/0x11-t10/.

Docker Privilege Escalation happens when a user gains unauthorized access to the docker group or exploits a misconfigured container. Because the docker daemon typically interacts directly with the host kernel and runs with high privileges, a user in the docker group can spawn a new container and mount the host machine's root file system into it, effectively granting them full root access over the entire host system.
More information on Docker privilege escalation techniques can be found through https://onsecurity.io/article/pentest-files-docker-breakout-2/.

---

## Initial Key Walkthrough

{{< figure src="1.png" >}}
Before tackling this room, we need to obtain a key from AoC'25 Room 1. The image above shows the clue provided to find this key. McSkidy left a text file mentioning that he hid three eggs (flag parts) around the room. Finding them unlocks a secret encrypted message in the documents directory. We are also given some account credentials to look around.

<br>

{{< figure src="2.png" >}}
With the provided account, I did some digging through the directories and found the first part of the flag inside `fix_passfrag_backups_20251111162432`. At the very end of the file, it reveals the first part: `3ast3r`.

<br>

{{< figure src="3.png" >}}
Secondly, I checked the Git repository and found a commit titled "sensitive notes". Exploring this commit revealed the second part of the flag: `-1s-`.

<div style="display: flex; gap: 10px; justify-content: center;" >
  {{< figure src="4.png" >}}
  {{< figure src="5.png" >}}
</div>

Lastly, it's a hidden file on the pictures directory where you can just write la -la to show it. The third flag is `c0M1nG`.

<br>

{{< figure src="6.png" >}}
Now that I had all the pieces, I could decode the encrypted file using the complete flag.

<br>

{{< figure src="7.png" >}}
The decrypted message reveals that there is a site where, if you submit the correct wishlist items, it returns a ciphertext. We then need to use OpenSSL to decode it and see what is inside.

<br>

{{< figure src="8.png" >}}
Let's start by creating a wishlist file containing the correct items.

<br>

{{< figure src="9.png" >}}
Next, I navigated to the directory containing the website files and ran it locally using a Python HTTP server.

<br>

{{< figure src="10.png" >}}
Voila, here is the website! I inputted the correct list, and it returned the ciphertext.

<br>

{{< figure src="11.png" >}}
{{< figure src="12.png" >}}
So, I saved the ciphertext in a text file and followed the OpenSSL instructions from the decrypted message to extract the hidden files. In that file is the final flag that is waiting for me to unlock the key to the side quest.

<br>

{{< figure src="13.png" >}}
{{< figure src="14.png" >}}
So, I used the same method to decrypt the file and copied the file to my desktop to see the picture.

<br>

{{< figure src="15.png" >}}
And finally got the key: `now_you_see_me`.

---

## Room Walkthrough

{{< figure src="16.png" >}}
To access this entire machine, you have to input the key `now_you_see_me` to unlock the memories.

<br>

{{< figure src="17.png" >}}
First up, reconnaissance. I ran a simple Nmap scan across all ports. It revealed multiple unknown open ports, meaning I had several places to investigate.

<br>

{{< figure src="18.png" >}}
I started by checking port 8080. It required a password, and standard methods like brute-forcing and common passwords doesn't work. I decided to pivot and check the other ports first. On port 8000, there was a "Fakebook" page. I made an account and started reading through everyone's social media posts.

<div style="display: flex; gap: 10px; justify-content: center;">
  {{< figure src="19.png" >}}
  {{< figure src="20.png" >}}
</div>

From the posts, I gathered some crucial OSINT. We learn that the guard's email is `guard.hopkins@hopsecasylum.com`. Now, we just needed to figure out his password. People tend to use words familiar to them, and I spotted four key pieces of information about the guard: Johnnyboy, Pizza1234$, 1982, and 43rd. I wrote a quick script to generate a custom wordlist combining these terms, and eventually found the correct password: `Johnnyboy1982!`.

<br>

{{< figure src="21.png" >}}
With those credentials, I logged into the web interface on port 8080. Just by successfully authenticating and pressing the cell/storage wing key button, I secured the first flag.

<br>

{{< figure src="22.png" >}}
Next, I visited port 13400. It prompted for a username and password, so I reused the credentials I just found, and it worked! I got into the portal. However, since I was just logged in as a guard, I lacked the permissions to access the admin camera (the psych ward exit camera).

<br>

{{< figure src="23.png" >}}
To bypass this, I intercepted the request and appended `?tier=admin` to the API endpoint.

<br>

{{< figure src="24.png" >}}
Refreshing the web page showed the actual video feed, revealing a hand punching in a code: `115879`.

<br>

{{< figure src="25.png" >}}
I entered the passcode and grabbed the first part of the second flag.

<br>

{{< figure src="26.png" >}}
{{< figure src="27.png" >}}
Also, if you noticed on there's always a get request on the page. So, I decided to download it and see what the file was. Turns out, the file has an intersting api endpoint: `/v1/ingest/diasnostics`

<br>

{{< figure src="28.png" >}}
{{< figure src="29.png" >}}
I sent a request (dont forget the ?tier=admin for authentichation bypass) and it returend another endpoint. This new endpoint revealed a token, port and status.

<br>

{{< figure src="30.png" >}}
{{< figure src="31.png" >}}
So, I tried to connect (nc) to the port the endpoint showed me (13404) and use the token to login. It worked, I connected as svc_vidops@tryhackme-2404 and the flag part 2 was just sitting on one of the files inside.

<br>

{{< figure src="32.png" >}}
{{< figure src="33.png" >}}
Then, it's time for privilege escalation. I checked for files with user permissions. Everything looks ordinary expect for one `diag_shell`.

<br>

{{< figure src="34.png" >}}
I inspected what `diag_shell` actually does and noticed it alters the UID and GID to execute `/bin/bash`. This was my ticket to escalate privileges.

<br>

{{< figure src="35.png" >}}
Executing the shell dropped me into the `dockermgr` user.

<br>

{{< figure src="36.png" >}}
{{< figure src="37.png" >}}
{{< figure src="38.png" >}}
As dockermgr user, i am in group "docker" but when i check my group after running the escelation, my groupo hasnt changed (still in svc_vidops)

<br>

{{< figure src="39.png" >}}
To fix this, I used the `newgrp docker` command to switch my active group, allowing me to run commands as the docker group.

<br>

{{< figure src="40.png" >}}
Since the docker group practically holds root privileges, I create a new container and mounted the host's root directory to the container's file system. This allowed me to browse all files on the host.

<br>

{{< figure src="41.png" >}}
Browsing through the host's root directory, I found a very interesting file called `scada_terminal`.

<br>

{{< figure src="42.png" >}}
{{< figure src="43.png" >}}
Inside the file was code/pin for the last flag and I just need to input it to the website

<br>

{{< figure src="44.png" >}}
And there it is! The final flag for the room.