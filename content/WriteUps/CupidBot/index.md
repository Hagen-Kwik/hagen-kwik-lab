---
title: "THM: CupidBot Writeup"
date: 2026-02-28
tags: ["THM", "Write Up","Prompt Injection"]
---

{{< figure src="featured.jpg" >}}

## Background Knowledge

This writeup is for `CupidBot`, a room in TryHackMe (THM) for Valentine's 2026 Event. This room focuses on Prompt Injection.

Prompt Injection is a vulnerabitlity where an attacker crafts malicious input to feed into the Large Language Model (LLM). The goal is to bypass its model's filters, resulting in an output/action that should otherwise be restricted. 


More information on Prompt Injection can be found through the OWASP official website https://owasp.org/www-community/attacks/PromptInjection.

---

## Walkthrough

{{< figure src="1.png" >}}
First, let's try the most direct approach, asking the bot to give us all the flags directly. Although it didn't work, it gave us a solid lead. The system responded with a specific code we could use to retrieve a flag.

<br>

{{< figure src="2.png" >}}
Then, I tried asking for the flag again, this time including the provided code. This successfully bypassed the filter, and the bot handed over the prompt injection flag.

<br>

{{< figure src="3.png" >}}
Next, I used the same method to ask for the system flag. Interestingly, instead of the flag itself, the bot returned instructions on how to obtain the administrator flag.

<br>

{{< figure src="4.png" >}}
Following that new lead, I used the provided method to request the administrator flag. The AI was a bit confused initially, so it took a couple of prompts to successfully override the filter. As you can see in the screenshot, I simply copy-pasted the AI's previous response back to it. The bot eventually understood the context and revealed the administrator flag which turned out to be the final flag.

<br>

{{< figure src="5.png" >}}
At this point, I was still missing one flag but I didn't know its exact name or format. So, I tried using wildcards (***) to fish out the last flag name. thankfully, it gave me the last flag format, the system prompt flag. Looking back, I realized I could have just asked the AI to list all the available flags from the very beginning instead of prying them out one by one! 

> [!TIP] Reflection
> Always review your methodology after completing a machine, as there are usually multiple ways to reach the objective. Remember to define your goals as clearly as possible from the start!-


{{< figure src="6.png" >}}
Once I knew the exact name, I simply asked the bot for the system flag, and it provided it right away.