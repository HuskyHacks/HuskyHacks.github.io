# eXternal XML Entity (XXE) Injection

## Video
[![External XML Entity Injection (XXE) | HuskyHacks | HackerOne Veterans in Security Community Day](http://img.youtube.com/vi/M68lwcC18kU/0.jpg)](http://www.youtube.com/watch?v=M68lwcC18kU "External XML Entity Injection (XXE) | HuskyHacks | HackerOne Veterans in Security Community Day")

## The Problem
XXE is interesting, to say the least.

First, we need to understand the second 'X' in XXE, which stands for Extensible Markup Language (XML). I do love those recursive acronyms.

XML is a markup language, much like its more popular and high-achieving cousin HTML. In fact, the two even look pretty similar if you compare them side by side:

````xml
<note>
  <to>Students</to>
  <from>HuskyHacks</from>
  <heading>Hello!</heading>
  <body>Hello, this is XML!</body>
</note>
````
````html
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Husky's Website</title>
</head>
<body>
    <p>Hello! This is HTML!</p>
</body>
</html>
````
So, the two must serve different purposes if they are different things, right? Absolutely.

HTML is designed to **interpret and display** data onto a web page. HTML has **pre-defined** tags that change how data renders and displays on a page.

XML, on the other hand, _doesn't really do anything_. Wait, what?

You read that right. If you put XML on a web page by itself, nothing happens. So what is the point?

XML can be used to **self-describe, tag, and carry data** to and from web pages. Think of XML as a quick way to write your own data scheme on a web page and tag different data.

Let's say you wanted to make a shopping list on an HTML page. HTML does not have a `<shopping-list>` tag, nor does it have  `<produce>` or `<snacks>`. But you can write your own XML scheme to have all of these things.

Consider a web application that is using XML to do just that:
````xml
<shopping-list>
  <produce>Lettuce</produce>
  <snacks>Pretzels</snacks>
  <baking>Baking powder</baking>
  <drinks>Seltzer</drinks>
</shopping-list>
````
Now, like I said earlier, XML _doesn't do anything_ by itself. The HTML of the page needs logic to identify these tags and handle them. Consider the following example of embedded Javascript code that is handling an XML transaction on a webpage:
````javascript
function loadDoc() {
    var req_params;
    var snack = "chips"
    req_params = "<shopping-list>\n";
    req_params = req_params + "<snacks>"+ snack + "</value>\n";
    req_params = req_params + "</shopping-list>\n";

    var xhttp;
    xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function() {
        if (xhttp.readyState == 4 && xhttp.status == 200) {
        xmlDoc = xhttp.responseText;
        txt = "";
        document.getElementById("demo").innerHTML = xhttp.responseText;
}
````
So, in a very simple sense, XML can tag and self describe data on a web page that HTML/Javascript can then handle. And the handling of this XML can be problematic.

## The Exploit
To absolutely no one's surprise, XML has some tricks up its sleeve, especially when its used on a web page.

XML can use `Entities` to translate a shortcut into a short-hand special character. Consider this example:

````xml
<!ENTITY site SYSTEM "https://huskyhacks.dev">
<author> &site; </author>
````
Once defined, the entity can live inside of an XML tag. The `site` entity now lives in the `<author>` tag and is defined with an ampersand (&) and ended with a semi-colon (;). So the XML above will dynamically insert the value of `site` between the `<author>` tags. In this example, the `SYSTEM` tag is used to define a private external entity, which could be a reference to a URL or some other kind of resource.

Nothing to worry about yet, right? Well, the real problem is that _external XML entities can be used to read files that are local to the webserver_ -facepalm-

How? Because you can point the entitiy to look at and read out the contents of a local file on the webserver. This is similar to Local File Inclusion (LFI) in that you, the user of a website, can get a web part to spit out the contents of a file on a server that you shouldn't have access to.

### Example
````xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [
  <!ELEMENT foo ANY >
  <!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
<foo>&xxe;</foo>
````
This XXE injection attack sets up an XML document, really just as a placeholder, and calls it `foo`. Then, it defines an entity called `xxe` and says "Entity, I'd like you to be whatever is located at `/etc/passwd` on this server". So what you end up with is an XML document that reads out the contents of the file that the entity is pointing to. See where I'm going with this?

Be on the lookout for web pages that have script elements and calls to the `XMLHttpRequest()` function. There are many other telltale signs that a website might be using XML to parse data, so make sure to use the **developer tools** and inspect a page's source code (in most web browsers, you can get here by hitting F-12).

As for performing the XXE injection itself, there's probably no better tool to use than **Burpsuite Community Edition** or Pro if you have it. Browse to a site that contains XML, look for a web part that is using the XML, and interact with the web part. Capture the interaction with Burp Intercept and then use the Burp Repeater to replace the page's XML content with your external entity injection.

## The Damage

If you, as the user, can inject this entity into a web page that is using XML to handle data, you can get the web page to spit out server-side file information. In the example above, the contents of `/etc/passwd` will get you a list of the users on the server, which can aid in enumeration. But on a weakly configured system, you might even be able to read the contents of `/etc/shadow` and get the server's password hashes.