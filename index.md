# The O-Course
An OWASP Top 10 Obstacle Course for Beginners by Matt Kiely (HuskyHacks)

#### Table of Contents  
- [Introduction](#) 
  - [Ten hut!](#ten-hut)
  - [Requirements](#requirements) 
  - [Objectives](#objectives)  
  - [Lab Environment Setup](#lab-environment-setup)
- [Tools](/tools/)
- [Vulns](/vulns/)
  - [Cross Site Scripting (XSS)](/vulns/xss.md)
  - [Structured Query Language Injection (SQLi)](/vulns/sqli.md)
  - [API Bruteforce and Information Disclosure](/vulns/api.md)
  - [eXternal XML Entity (XXE) Injection](/vulns/xxe.md)
  
  <!-- toc -->


## Ten hut!

Alright, recruits! Welcome to the O-Course. Glad to have you.

My name is Sergeant Husky, and for the next hour or so, I’ll be leading you through the course. You will be doing two things here: learning, and hacking! Sound good?

You will be presented with a series of web application obstacles, and you’ll have the opportunity to learn about how they work, how they can be exploited, and then you can hack them to your heart’s content!

You want to be the greatest bug bounty hunter on Earth, do ya? Well, I would say that’s an admirable goal! But you gotta start with the basics, and you’ll learn the basics here. 

### Requirements

I ask that you bring two things to this course:

- Yourself, ready to learn, and
- A **fresh install of the latest build of Kali Linux**

### Objectives

When you leave this course, I’m hoping you’ll be able to do these things (in the biz, we call these “Learning Objectives):

- Identify, understand, and exploit **Cross Site Scripting (XSS)**
-	Identify a SQL Injection point, perform **manual and autopmated SQL injection**, to gain unauthorized access to a backend database.
-	Identify and exploit an **XML External Entity vulnerability** to gain access to a web server’s file system.
-	**Bruteforce an API** and use it to gain access to information.
-	Use tools like **Burp suite, dirbuster, SQLMap, XXS Strike, Nmap**, and others and begin to gain mastery with them.
-	And, of course… **have fun and look cool while doing it**!

### Lab Environment Setup

Well, if we're gonna be hacking and learning, we definitely need something to hack and learn about, right? Don't worry, I have you covered. This course comes complete with an entire web application, front-end and back-end, that is created locally just for you!

In your fresh install of Kali Linux:

1. Right-click on the Desktop and select "Open Terminal Here"
2. In the command prompt, enter the following: `cd /opt && sudo git clone https://github.com/HuskyHacks/O-Course`
3. When that has finished, in the command prompt, enter the following: `sudo chmod 755 -R /opt/O-Course && cd O-Course && sudo python3 install.py`
4. Follow the script's prompts (hit enter a few times) until it is done. The final part of the script launches the docker web app.
5. Browse to `172.17.0.1` to launch the course!

## 
