+++
date = '2025-04-13T05:05:08+05:30'
draft = false
title = 'My Cybersecurity Journey: Kicking Off with HTB CPTS Certification'
comments = true
description = "Join me on my journey to cybersecurity, starting with the HTB certification. I’ll document the tools, techniques, and methodology I use as I progress through the challenges and certifications."
tags = ["HTB", "Cybersecurity", "Penetration Testing", "CPTS", "CBBH", "PNPT", "OSCP"]
author = 'Koustubha naik (Z3KAIx0)'
+++

# 'My Cybersecurity Journey: Kicking Off with HTB CPTS Certification'

Hey there! This is my first blog post, and I’m officially diving🤿 into the world of cybersecurity—with the **Hack The Box (CPTS) certification** as my first step.

## Why HTB CPTS?

![HTB CPTS](/posts/letsgo/cpts.png)


Honestly? It’s **only $210**, and the **certificate design is cool as f\*\*\***(Design OP🙃). Hack The Box gives you hands-on experience in a controlled environment where you can test real-world hacking techniques. It’s not just about theory—it's about actually **doing**.Hacking ain’t about ticking boxes ✅ — it’s about figuring stuff out like a boss 😂😂

Say what you want, but all the HTB certs look crazy cool 🔥🔥

<div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%;">
  <iframe src="https://www.youtube.com/embed/89dllajbcJk?autoplay=0" 
          frameborder="0" 
          allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" 
          allowfullscreen 
          style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
  </iframe>
</div>

---

## Certification Roadmap

I’m not picking certs randomly—these things cost 💰, and I’ve still got OSCP on my radar. So yeah, it is what it is 🙃. If u want cybersecurity certification roadmap (means u does't know which cert to take first after that which )its down here 👇👇👇

🔗 [Paul Jerimy's Security Certification Roadmap](https://pauljerimy.com/security-certification-roadmap/)

This roadmap is a visual guide that maps out various cybersecurity certifications. If you're looking for a certification path in any field—offensive security, risk management, networking, etc.—everything's there.

**Fun Fact 👉** After visiting that website, I got to know about **OSEE** 😳 **OSEE**   

![Paul Jerimy’s Security Certification Roadmap](/posts/letsgo/security-certification-roadmap.png)
<p style="text-align: center;">*This screenshot showing how i will gooooo ...*</p>

---

## My Study Plan 
 
 This is nothing fancy—just how I broke down all the **CPTS** topics into parts that make sense for me—helps me stay on track and not feel overwhelmed.
 
### **Part 1: Getting Started**

| Topic | What It Means |
|-------|----------------|
| Penetration Testing Basics | This is where you learn the fundamentals of hacking. You need to understand the mindset behind penetration testing—how to approach testing systems for vulnerabilities and how hackers exploit them for ethical reasons. |
| Using Nmap to Find Computers | **Nmap** is your go-to tool for scanning networks. It helps you identify live hosts, open ports, and services on a network, giving you a blueprint of what you're dealing with. |

---

### **Part 2: Finding Information**

| Topic | What It Means |
|-------|----------------|
| Footprinting | This is the first step in gathering information about a target. It involves looking up domain names, email addresses, social media profiles, and public databases to gather as much information as possible. |
| Info from Websites | By scraping or manually collecting data from a target’s website, you can uncover clues about their internal network, technologies they use, and even hidden vulnerabilities. |

---

### **Part 3: Finding Weaknesses & Exploiting Them**

| Topic | What It Means |
|-------|----------------|
| Finding Weaknesses | Vulnerability scanning is all about identifying flaws in a system that can be exploited. This involves using automated tools and manual techniques to find weaknesses in a system's defenses. |
| Transferring Files | When conducting a penetration test, transferring files like payloads and scripts between systems is necessary. This can be done through different methods like HTTP, FTP, or SMB. |
| Shells & Payloads | After finding vulnerabilities, the next step is to establish a shell or payload that gives you control over the system. Tools like **Netcat** or **Metasploit** make this process easier. |

---

### **Part 4: Hacking Services & Passwords**

| Topic | What It Means |
|-------|----------------|
| Using Metasploit | **Metasploit** is one of the most powerful tools in a penetration tester’s arsenal. It helps automate the exploitation of vulnerabilities, making the process quicker and more efficient. |
| Guessing Passwords | Brute-forcing passwords is a classic attack. Tools like **Hydra** or **Burp Suite** can help automate this process by testing common passwords or dictionary-based attacks. |
| Breaking into Services | Services like **SSH**, **FTP**, or **RDP** are often the targets of brute-force or known-vulnerability attacks. By compromising these services, you can gain access to a system. |

---

### **Part 5: Hacking Websites**

| Topic | What It Means |
|-------|----------------|
| Attacking Websites with Ffuf | **Ffuf** is a fast web fuzzer used to discover hidden directories, files, or other resources on a website that might be vulnerable to attack. |
| SQL Injection | **SQL Injection** is one of the oldest but still effective attacks on websites. By manipulating database queries, attackers can retrieve sensitive information from a database. |
| SQLMap | **SQLMap** automates the process of exploiting SQL injection flaws. It’s a powerful tool for anyone who needs to perform database-based attacks. |
| Cross-Site Scripting (XSS) | **XSS** attacks allow attackers to inject malicious scripts into web pages viewed by others. This is a common attack method for stealing user credentials or session tokens. |
| File Attacks | Uploading malicious files (like web shells) to a server is another way to compromise a site. Once the file is executed, you can gain access to the system. |
| Command Injection | Command injection attacks occur when a web application executes system commands from user input. By manipulating this input, you can execute arbitrary commands on the server. |

---

### **Part 6: Gaining Control** 

| Topic | What It Means |
|-------|----------------|
| Linux Privilege Escalation | This is about gaining higher-level access on Linux systems. Common techniques involve exploiting misconfigurations or using local exploits. |
| Windows Privilege Escalation | Privilege escalation on Windows systems often involves exploiting vulnerable services or insecure permissions to gain admin-level access. |
| Pivoting & Port Forwarding | After compromising a system, you may need to pivot to other systems on the network. **Port forwarding** lets you access systems hidden behind firewalls or NATs. |

---

### **Part 7: Writing Reports & Big Targets**

| Topic | What It Means |
|-------|----------------|
| Writing Reports | A major part of penetration testing is documenting your findings. This involves creating detailed reports of vulnerabilities found and suggesting mitigation strategies. |
| Hacking Big Networks | Penetration testing isn’t just for small systems. You’ll need to know how to assess large enterprise networks, dealing with things like Active Directory, firewalls, and other complex security infrastructure. |

---

## What's Next?

So far, played a few beginner boxes on HTB, and it’s realy fun !! ( until or unless u get it 😂 ). The process is been all about scanning, finding weak spots, and exploiting them. THEN BOOM 💥

Each challenge teaches something new, and i would like to dive deeper. Once I pass HTB **CPTS**, I’ll move on to **CBBH**, **PNPT (maybe)**, and eventually **OSCP**. Not sure how i will go but its going to start with **CPTS** and not end with **OSCP** because lets decide what to do after **OSCP** 😄

---

Feel free to follow along with my journey, drop a comment if you have any tips, or let’s discuss what’s working for you!
