---
title: Username enumeration via subtly different responses ( Auth Lab 2 )
date: 2026-02-03
categories: [portswigger,Authentication]
---

**lab link :** [link](https://portswigger.net/web-security/learning-paths/authentication-vulnerabilities/password-based-vulnerabilities/authentication/password-based/lab-username-enumeration-via-subtly-different-responses#)

**Module :** Authentication Vulnerabilities

**Difficulty :** Practitioner


**Tool used :** Burp Suite community edition

**credintials :** [username](https://portswigger.net/web-security/authentication/auth-lab-usernames) & [password](https://portswigger.net/web-security/authentication/auth-lab-passwords)


---

**Steps :**

<ins>**Step1**</ins>: Type a random username and password in login portal and inspect using burpsuite proxy

<ins>**Step2**</ins>: forwarded the request to the burp intruder .

<ins>**Step3**</ins>: In burp intruder highlight the username and click **Add §** , the payload is selected and make sure the payload is in **simple list**.

![](/assets/images/auth-lab-2/2026-02-22_13-23.png)

<ins>**Step4**</ins>: copy&paste the username payloads in the payload configuration section.

<ins>**Step5**</ins>: Go to settings section. see for **Grep-Extract** , click **Add** and wait for the response message that appears in dialog box, search the error message **"Invalid username or password."**. using the mouse highlight the text and click ok  

![](/assets/images/auth-lab-2/2026-02-22_13-27.png)

<ins>**Step6**</ins>: Start the attack , after the completeion of the attck, notice the one packet will be responded differentently.

![](/assets/images/auth-lab-2/2026-02-22_13-28.png)

<ins>**Step7**</ins>: send that packet to the intruder section , select the password parameter and click **Add §** , the payload is selected and make sure the payload is in **simple list**.

![](/assets/images/auth-lab-2/2026-02-22_13-29.png)

<ins>**Step8**</ins>: copy&paste the password payloads in the payload configuration section.

<ins>**Step9**</ins>: After the attack is finished, examine the status code in result tab. we can sort the status code by using column header.

![](/assets/images/auth-lab-2/2026-02-22_13-30.png)

**Note :** Each packet will responded with **200 code** except one, which we noticed  **302 response code**.