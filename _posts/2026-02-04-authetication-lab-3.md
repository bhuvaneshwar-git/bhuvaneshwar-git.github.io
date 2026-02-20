---
title: Username enumeration via response timing ( Auth Lab 3 )
date: 2026-02-04
categories: [portswigger,Authentication]
---

Tool used - Burpsuite community edition

Credintials - wiener:peter 

---

<ins>**Step 1**</ins> : For experiment i tried four times with wrong password server is blocking my ip address .

![](/assets/images/2026-02-04_22-21.png)

<ins>**step 2**</ins> :  **X-Forwarded-For** used to spoof the ip address and bypass the ip based brute force protection.

<ins>**Step 3**</ins> : using **responder** i manually checked response time from server by doubling the password length 

![](/assets/images/2026-02-04_22-25.png)

![](/assets/images/2026-02-04_22-27.png)

![](/assets/images/2026-02-04_22-28_1.png)

<ins>**Step 4**</ins> : I forwareded the request to **intruder** for finding username using brute force attack 

username wordlist - [Username](https://portswigger.net/web-security/authentication/auth-lab-usernames)

<ins>**Step 5**</ins> : for this attack i used **pitch forck attack**, select **X-Forwarded-For** and **username** for brute forcing .

![](/assets/images/image1.png)

<ins>**Step 6**</ins> : selct the payload 1 type as **number** and for number range i have given from **5** to **105**

<ins>**Step 7**</ins> : For payload 2 type as list and paste the username list from the provider.

<ins>**Step 8**</ins> : One responce took more time than others it like to be a correct username

![](/assets/images/image2.png)

<ins>**Step 9**</ins> : copy the username and go back to the **intruder** section clear selected payloads, add $ to  **X-Forwarded-For** and **password**. 

<ins>**Step 10**</ins> : for payload 1 type as **number** and for number range i have given from **5** to **105**

<ins>**Step 11**</ins> : For payload 2 type as list and paste the password list from the provider.

username wordlist - [Password](https://portswigger.net/web-security/authentication/auth-lab-passwords)

<ins>**Step 12**</ins> : If the credintials is correct server repond with **302** code .

![](/assets/images/image3.png)

