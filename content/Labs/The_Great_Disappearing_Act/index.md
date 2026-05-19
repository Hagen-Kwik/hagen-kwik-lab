---
title: "THM: The Great Disappearing Act Writeup"
date: 2026-03-03
tags: ["THM", "Write Up", "JWT"]
---

{{< figure src="featured.jpg" >}}

## Background Knowledge

This writeup is for `The Great Disappearing Act`, a room in TryHackMe (THM) for Advent of Cyber (AoC) 2025 Side Quest's Event. This room focuses on .

---

## Initial Key Walkthrough

{{< figure src="1.png" >}}
Before trying this room, you have to obtain a key from AoC'25 room number 1. The picture above is the clue in the room to find the key. mcskidy wrote a text file stating that he hid 3 eggs (flag parts) in this room, that flag will unlock a secret message you should solve that is encryped in the rooms documents directory. Also he give an account to look around.

<br>

{{< figure src="2.png" >}}
With the given account, i did some looking around in the directories and fgound the first part of the flag on fix_passfrag_backups_20251111162432. In the end of the file is gives the first part of the flag, `3ast3r`

<br>

{{< figure src="3.png" >}}
second, i opened the git repository and found a commit that is titled "sensitive notes". So I checked the commit and found the second part of the flag which is `-1s-`

<br>

<div style="display: flex; gap: 10px; justify-content: center;" >
  {{< figure src="4.png" >}}
  {{< figure src="5.png" >}}
</div>
lastly is fairly simple, its a hidden file on the pictures directoty where you can just write la -la to show it. the third flag is `c0M1nG`

<br>

{{< figure src="6.png" >}}
so after i found all the parts, i can decode the encrypted file with the given flag

<br>

{{< figure src="7.png" >}}
Here is the decrypted message. it says that there is a site that when you put the correct wishlist items. the site will return a ciphertetxt, and use openssl to decode it and see whats inside.

<br>

{{< figure src="8.png" >}}
so lets start by making a wishlist file containing the correct items

<br>

{{< figure src="9.png" >}}
then i go to the directory that has the website files and run it locally usng python server

<br>

{{< figure src="10.png" >}}
voila heres the website. i input the correct list and it returend me the cipher text

<br>

<div style="display: flex; gap: 10px; justify-content: center;" >
  {{< figure src="11.png" >}}
  {{< figure src="12.png" >}}
</div>
then i saved the cipher text on a text file and followed the openssl isntruction from the decrypted message to extrtact the hideden files. In that file is the final flag thats waiting for me to unlock the key to teh side quest

<br>

<div style="display: flex; gap: 10px; justify-content: center;" >
  {{< figure src="13.png" >}}
  {{< figure src="14.png" >}}
</div>
so i used the same method to decrypt the file and copied the file to my desktop to see the picture

<br>

{{< figure src="15.png" >}}
and finally i get my key. `now_you_see_me`

---

## Room Walkthrough

{{< figure src="16.png" >}}
To access this entire machine, you have to input the key which is now_you_see_me taht will unlock the memories.

<br>

{{< figure src="17.png" >}}
first is to alway do recoinassance. i did a simple nmap of all ports, it gave me multiple opened unkowon potrts. so i have miultiple palces i have to search

<br>

{{< figure src="18.png" >}}
so checked port 8080. but it required a password and nothing worked(burte force, guessing) so i decided to check other ports first. in port 8000 thers a fakebook. i made an account and i saw all the social media posts everyone was posting

<br>

<div style="display: flex; gap: 10px; justify-content: center;">
  {{< figure src="19.png" >}}
  {{< figure src="20.png" >}}
</div>

as we can see from the posts(OSINT), i have gathereed enough infomration. we know that the email is `guard.hopkins@hopsecasylum.com`. and now we just need to giess the ppssword. Johnnyboy, Pizza1234$, 1982, 43rd, here are 4 informaton about the guard. since people use words familiar to them, i made a script that can make a combination of passwords using those 4 words and in the end i got the correct password which is`Johnnyboy1982!`.

<br>

{{< figure src="21.png" >}}
so i logged in port 8080 web using the password i found. then by just knowing the passwod, i got the first flag. by simply pressing the cell/storage wing key.

<br>

{{< figure src="22.png" >}}
next, i went to port 13400, it ask for password and username so i inputed the same one i found earlier and it worked. i got into this portal. however im just a guard so i was not able to access the one admin camera which is psych ward exit camera

<br> 

{{< figure src="23.png" >}}
so i intecepted the request and added the ?tier=admin on the endpoint to bypass authentication

<br> 

{{< figure src="24.png" >}}
then i rechecked the web and it reloaded and showed the real video, showing ahadn punching in the code. it is `115879`

<br>

{{< figure src="25.png" >}}
then i entered the passcode and got the second flag part 1

<br>

<div style="display: flex; gap: 10px; justify-content: center;" >
  {{< figure src="26.png" >}}
  {{< figure src="27.png" >}}
</div>
also if you noticed on theres always a get request on the page. so i decided to download it and see what the file was, and i checked the file and found an intersting api endpoint.it was /v1/ingest/diasnostics

<br>

<div style="display: flex; gap: 10px; justify-content: center;" >
  {{< figure src="28.png" >}}
  {{< figure src="29.png" >}}
</div>
so i tested them, i send a request (dont forget the ?tier=admin) and it returend another endpoint. so i checked the new endpoint and got a token, port and a status.

<br>

<div style="display: flex; gap: 10px; justify-content: center;" >
  {{< figure src="30.png" >}}
  {{< figure src="31.png" >}}
</div>
so next i tried to connect to the port it gave me (13404) and use the token to login and it worked i got loged in as svc_vidops@tryhackme-2404. and a simple ls gaveme the seocnd flag part 2.

<br>

<div style="display: flex; gap: 10px; justify-content: center;" >
  {{< figure src="32.png" >}}
  {{< figure src="33.png" >}}
</div>
then its time for privilege escalation. i checked for files with user permissions so i can run them. ervythging looks ordinary expect for one diag_shell.

<br>

{{< figure src="34.png" >}}
so i checked what diag_shell file is about. and i see that they have change the gid and uid and bin/bash function. this is my ticket to escelation.

<div style="display: flex; gap: 10px; justify-content: center;" >
  {{< figure src=".png" >}}
  {{< figure src=".png" >}}
</div>
then its time for privilege escalation. i checked for files with user permissions so i can run them. everything looks ordinary expect for one diag_shell.

<br>

{{< figure src="3.png" >}}
so i checked what diag_shell file is about. and i see that they have change the gid and uid and bin/bash function. this is my ticket to escelation.