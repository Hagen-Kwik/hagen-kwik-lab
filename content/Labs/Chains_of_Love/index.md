---
title: "THM: Chains of Love Writeup"
date: 2026-03-02
tags: ["THM", "Write Up","SSRF","JWT","Security Misconfiguration"]
---

{{< figure src="featured.jpg" >}}

## Background Knowledge

This writeup is for `Chains of Love`, a room in TryHackMe (THM) for Valentine's 2026 Event. This room focuses on Server-Side Request Forgery (SSRF), JWT exploitation, and security misconfigurations leading to information disclosure via exposed Git repositories.

### JSON Web Token (JWT)
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

### Server-Side Request Forgery (SSRF)
SSRF vulnerability allows an attacker to force a server to make requests to an unintened location. Attacker adds a target URL on the request to trick server to access them. This attack is often used to bypass firewall or access control lists so they can access internal servers.

More information on SSRF can be found on OWASP's official website https://owasp.org/www-community/attacks/Server_Side_Request_Forgery.

### Information Disclosure (Exposed .git)
A security misconfiguration is when a system is set up incorrectly, creating vulnerabilities. In this case a .git directory is left accessible on a public web server. This allows attackers to download the entire source code history, including sensitive configuration files, credentials, and previous commits.

---

## Walkthrough

{{< figure src="1.png" >}}
First, to access the application using the hostname, I had to manually map the IP address to `nova.thm` in my `/etc/hosts` file.

<br>

{{< figure src="2.png" >}}
And voila, this is the first thing you see on the website. After scouring the site, nothing was intersting yet.

<br>

{{< figure src="3.png" >}}
So, I moved on to do more reconnaissance using directory fuzzing. Here, I found an interesting lead, an exposed .git/HEAD file. This is a critical security flaw, as no git repository files should ever be publicly accessible on a production server.

<br>

<div style="display: flex; gap: 10px; justify-content: center;" >
  {{< figure src="4.png" >}}
  {{< figure src="5.png" >}}
</div>
Now that i know there's a git repository, I used git-dumper to download the entire repository.

<br>

{{< figure src="6.png" >}}
I scoured through the commit history and found an interesting commit related to a `preview_feature`. The file contained an web application route function for one of the website's pages, revealing that submitting {{config}} would return the application's configuration settings.

<br>

<div style="display: flex; gap: 10px; justify-content: center;" >
  {{< figure src="7.png" >}}
  {{< figure src="8.png" >}}
</div>

So I tested this out, and it dumped a list of configuration settings. While most were default values, one stood out which is `admin_secret`. This is not a standard configuration, so I dug deeper to see what it could be used for.

<br>

{{< figure src="9.png" >}}
After checking my nmap scans from earlier, I revisited an admin login page. By inspecting the page source, I found an HTML comment stating they "use JWT now." Connecting the dots, I realized the admin_secret was likely the secret key used to sign their JWTs.

<br>

{{< figure src="10.png" >}}
I experimented with the secret key and forged my own session token. After some trial and error, I discovered the application only required two payloads, username and role. I used the official JWT website to craft the token properly.

<br>

{{< figure src="11.png" >}}
I injected my browser with the forged session token, I refreshed the page and successfully bypassed authentication, gaining access to the admin dashboard.

<br>

{{< figure src="12.png" >}}
I explored all the features, but only the `Internal QA URL Fetch` tool worked. It had strict filters, it only accept domain names and rejects any input containing numbers.

<br>

{{< figure src="13.png" >}}
Since the tool specifically mentioned an internal URL, I suspected there might be a hidden subdomain. I ran another directory/subdomain scan and discovered the internal subdomain.

<br>

{{< figure src="14.png" >}}
I used the QA URL Fetch tool to access the internal subdomain and found a Python sandbox. Strangely, the run button didn't work, and nothing executed when I clicked it.

<br>

{{< figure src="15.png" >}}
I realized the button didn't work because the sandbox was trying to execute code from my browser's perspective, but we are accessing it through SSRF. To execute code, the payload needed to be passed directly into the URL parameter through the admin page's fetch tool, forcing the server to make the request and execute the code on our behalf.

<br>

{{< figure src="16.png" >}}
Now that it runs commands, I have to find the flag file. Since it's a CTF, usually the file is called flag.txt or something similar. So I tried it and it worked, but the output wasn't correct.

<br>

{{< figure src="17.png" >}}
I tried multiple methods to open the file, but kept running into errors and filters. After tweaking my payloads, I finally crafted a working one that successfully bypassed all the restrictions.

<br>

{{< figure src="18.png" >}}
And finally, the payload executed cleanly, giving me the flag.