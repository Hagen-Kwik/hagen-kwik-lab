---
title: "THM: TryHeartMe Writeup"
date: 2026-02-26
tags: ["THM", "Write Up"]
---

> [!TIP] Fun Fact
> This is my first Writeup on my personal website. Hope you enjoy!

{{< figure src="featured.jpg" >}}

## Background Knowledge

This writeup is for `TryHeartMe`, a room in TryHackMe (THM) for Valentine's 2026 Event. This room focuses on JWT manupulation.

A JWT (JSON Web Token) is a cryptographic signature specifically generated for each website visitor. It helps the website remember who you are and is typically stored in the cookie for authentication, authorization and session management. A standard JWT token consist of 3 parts. They are header, Payload and Signature.

<b>Header:</b>
<br><b>alg</b> – the cryptographic algorithm used to sign the token (HMAC with SHA-256 or RSA with SHA-256).
<br><b>typ</b> – the type of the token, which is usually "JWT".

Payload:
<br><b>exp</b> - expiration time of token before the token becomes invalid.
<br><b>iat</b> - issued at time.
<br><b>iss</b> - the issuer of the token.
<br><b>sub</b> - the subject, which is often the user ID.
<br><b>others</b> - anything else the developers wants to put in.

<b>Signature:</b>
<br><b>signature</b> - Base64-encoded header and payload + secret key is signed with the algorithm specified in the header.

You can experiment with decoding and modifying your own JWTs on their official website https://www.jwt.io/.

---

## Walkthrough

{{< figure src="1.png" >}}
The first thing to do when approaching a target machine is reconnaissance. Always look around the target and check functionalities or features that might be useful for your attack later on. I started by creating an account, logging in, attempting to make a purchase, and clicking on all the products in the shop to understand how the website operates.

<br>
{{< figure src="2.png" >}}

After exploring the website, I found something interesting in Burp Suite while checking the network requests. The application set a cookie named `tryheartme_jwt`. This serves as a massive clue, as standard session cookies do not usually have such a specific, JWT-related naming convention. It seemed like the developers were pointing directly toward a JWT manipulation path.

<br>
<div style="display: flex; gap: 10px; justify-content: center;" >
  {{< figure src="3.png" >}}
  {{< figure src="4.png" >}}
</div>

I decided to decode that token and discovered that the developer stored the user's role and credits directly inside the JWT payload. This is a possible attack vector to persue since there are two critical variables in the payload(`role` and `credits`). So I decided to try modifying both. 

My thought process was simple: I am currently a standard user, so the first step is to elevate my privileges. This might allow me to see the `valenflag` product, the hidden item. I guessed the role `admin` because it is a common naming convention developers use for elevated access. For the credits, it is just a matter of changing the integer value higher so I can afford the items.


<br>{{< figure src="5.png" >}}
Since I want to find the hidden product, the first place to test this is by reloading the website while injecting my newly forged admin token.


<br>{{< figure src="6.png" >}}
And voila, it worked! Turns out the signature verification is completely missing. The backend only checks the payload contents without verifying the cryptographic signature. Now that the system recognizes me as staff (admin), I can finally see the valenflag. Let's try to buy it.

<br> {{< figure src="7.png" >}}
Do not forget, though: after clicking on the product, you have to intercept and modify the token again because the forged token is not permanently saved in your browser.

<div style="display: flex; gap: 10px; justify-content: center;">
  {{< figure src="8.png" >}}
  {{< figure src="9.png" >}}
</div>
Now, simply repeat the process of replacing the original token with your forged admin token every time you perform an action on the website, such as pressing the buy button.

<br>{{< figure src="10.png" >}}
After completing the buying process, the website finally generates a receipt. If you look closely at that receipt, you will find your flag!


