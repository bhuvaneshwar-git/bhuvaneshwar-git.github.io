---
title: Username enumeration via different responses ( Auth Lab 1)
date: 2026-02-02
categories: [portswigger,Authentication]
---

**lab url :** [Lab Link](https://portswigger.net/web-security/learning-paths/authentication-vulnerabilities/password-based-vulnerabilities/authentication/password-based/lab-username-enumeration-via-different-responses)

**Module :** Authentication Vulnerabilities

**Difficulty :** Apprentice

**Tool used -** caido

username wordlist - [Username](https://portswigger.net/web-security/authentication/auth-lab-usernames)

Password wordlist - [Password](https://portswigger.net/web-security/authentication/auth-lab-passwords)

---

## Steps
<ins>**Step 1**</ins>: Type invalid username and passowrd in login page and inpect using caido 

<ins>**Step 2**</ins>: In caido , go to **HTTP history** forward **post /login** request to caido automate.

![](/assets/images/auth-lab-1/2026-02-07_18-51.png)

<ins>**Step 3**</ins>: In caido automate, select username parameter and click **+ icon** , the payload is selcted and make sure the payload type is in **simple list** .Finally run the attack.

![](/assets/images/auth-lab-1/2026-02-07_18-46_1.png)

<ins>**Step 4**</ins>: After the attack is finished, examine length column in the result tab. you can sort the length using the column header. **Note:** that one packet length will be higher compare to other packets.   

![](/assets/images/auth-lab-1/2026-02-07_18-46.png)

<ins>**Step 5**</ins>: right click forward the packet to caido automate and selct the password parameter and click **+ icon** , the payload is selcted and make sure the payload type is in **simple list** .Finally run the attack.

![](/assets/images/auth-lab-1/2026-02-07_18-47.png)

<ins>**Step 6**</ins>: After the attack is finished, examine the status code in result tab. we can sort the status code by using column header. 

**Note :** Each packet will responded with **200 code** except for one, which got **302 response code**

![](/assets/images/auth-lab-1/2026-02-07_18-45.png)

<ins>**Step 7**</ins>: Login using username and password that was found.

![](/assets/images/auth-lab-1/2026-02-07_18-44.png)
