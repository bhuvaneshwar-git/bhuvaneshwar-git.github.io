---
title: HTB CTF 1
date: 2026-02-04
author: Bhuvaneshwar G
categories: [CTF]
tags: [htb] # tag should be always in lowercase
---

# CTF 1

Scanning and Enumeration 

```bash
sudo nmap -sV -sC 10.129.27.51
```

![My image](/assets/images/Pasted%20image%2020260124174853.png)

found two credintials 

![](/assets/images/Pasted%20image%2020260124175022.png)

retrived admin password -  rKXM59ESxesUFHAd

url - http://10.129.27.51/login.php

username - admin
password - rKXM59ESxesUFHAd

Flag:

![](/assets/images/Pasted%20image%2020260124175141.png)

