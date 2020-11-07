# Application Programming Interface (API) Bruteforce and Information Disclosure

- [Introduction](../index.md) 
  - [Ten hut!](../index.md#ten-hut)
  - [Requirements](../index.md#requirements) 
  - [Objectives](../index.md#objectives)  
  - [Lab Environment Setup](../index.md#lab-environment-setup)
- Tools
  - [FoxyProxy and Burpsuite](/tools/burpsuite)
  - [SQLmap](/tools/sqlmap)
- Vulns
  - [Cross Site Scripting (XSS)](/vulns/xss)
  - [Structured Query Language Injection (SQLi)](/vulns/sqli)
  - [API Bruteforce and Information Disclosure](/vulns/api)
  - [eXternal XML Entity (XXE) Injection](/vulns/xxe)
## Video
[![API Bruteforce | HuskyHacks | HackerOne Veterans in Security Community Day](http://img.youtube.com/vi/ShFalzCf8PQ/0.jpg)](http://www.youtube.com/watch?v=ShFalzCf8PQ "API Bruteforce | HuskyHacks | HackerOne Veterans in Security Community Day")

## The Problem
This is an interesting one because, by and of itself, this is not necessarily a vulnerability in the same way that Cross-site Scripting and SQL Injection are vulnerabilities. Really, this is just a matter of using something for its intended purpose that maybe shouldn't be there in the first place.

Application Programming Interfaces (APIs) are meant to make life easier. So what's the big problem?

APIs are the access points of larger, more complex functions that are happening behind the scenes on a website. They are publicly exposed and offer an access point based on a request-response system. Depending on what the API is actually designed to do, they can be absolute goldmines of information. 

I used one recently that illustrates how APIs can be useful: I sent the entire works of William Shakespeare into a language analysis API to get the sentiment of Shakespeare's works (https://huskyhacks.dev/2020/04/28/shake-down/). I scripted something to make a web request to a website that had a language sentiment analysis function on the back end and it did all the heavy lifting for me. 

APIs are as varied as data itself! Some tell the weather, some locate coordinates on a map, some give away the phone numbers and email addresses of customers of a website... wait, what?

## The Exploit
Again, this is not so much a vulnerability like XSS or SLQi where something is improperly sanitized or coded incorrectly. This is an Information Disclosure vulnerability in which you, the super-hacker sleuth, search thoroughly through a choice website and find API access points that maybe shouldn't be there. And if you happen to find these access points, it's totally possible to make requests to the APIs that end up spitting out sensitive information.

I think Heath Adams, aka TheCyberMentor, has one of the best practical examples of this in a recent YouTube video. Check it out here:

[![Real Bugs - API Information Disclosure](http://img.youtube.com/vi/X_JTdIkfKow/0.jpg)](http://www.youtube.com/watch?v=X_JTdIkfKow "Real Bugs - API Information Disclosure")

It is hard to describe exact step-by-step of how to exploit this kind of vulnerability because APIs are so diverse and varied. But, with a little assistance from TheCyberMentor up there, we can piece together a solid methodology for identifying these endpoints and hopefully figuring out how to manipulate them into information disclosure.

#### Example 

1. Thorough enumeration of the target website is an absolute must. Once you have identified the in-scope addresses of the website, start looking for APIs. Grab an API endpoint wordlist (https://github.com/chrislockard/api_wordlist) and use a tool like `dirb`,`gobuster`, and/or the Burp Intruder to fuzz and brute-force the website for API endpoints.
2. When you have located an API endpoint, try to identify if there is any documentation available for it. Maybe this API is meant to be accessed regularly, and documentation is easily available. Maybe this is an older, forgotten API that still functions. This second class of APIs has very high potential for information disclosure.
3. Start by interacting with the API. Make `GET` and `POST` requests to the access point with `curl` or the Burp Repeater. Examine how the API responds to certain requests. The errors from ineracting with the API may give away clues as to how it fuctions and what you need to do.
i.e.`curl http://site.local/api/v1/account/` to perform a basic`GET` request without parameters and `curl -X POST http://site.local/api/v1/account/` to make a `POST` request without parameters.
4. Remember, you're trying to figure out "What can I make this do?" not "What does this do?" Does the API need parameters passed to it? Try changing the values around and feed it something it might not expect.

## The Damage
I'd say it's unlikely, but not impossible, to get any kind of remote code execution off of a basic API. But, because these things are so varied, you never know.

The more likely scenario is **information disclosure**, where the API is used to mine data from the web application that probably shouldn't be accessible.