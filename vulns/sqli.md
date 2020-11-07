# Structured Query Language Injection (SQLi)

#### Table of Contents  
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
  
  <!-- toc -->
  
## Video
[![Structured Query Language Injection | HuskyHacks | HackerOne Veterans in Security Community Day](http://img.youtube.com/vi/ViR5z82b2Po/0.jpg)](http://www.youtube.com/watch?v=ViR5z82b2Po "Structured Query Language Injection | HuskyHacks | HackerOne Veterans in Security Community Day")

## The Problem
SQL injection was, without a doubt, the first web attack I saw and thought "Ok, I get it now."

SQL injection is a perfect summary of why web applications are always going to be vulnerable. Its principle thesis is that when you put complex systems together and try to make them work in concert, sometimes there are unintended problems.

Structured Query Language is a family of different database syntax. It is the primary means by which someone will interact wiht a back-end database, much like HTML is the primary language that someone will use to build a website.

SQL, in my opinion, has one of the most straightforward language structures in the entire field.
 
 If you have a database table called "Student" and you want to select all of the records in the table called "Student" where the name is "Matt", the syntax is
 ````sql
SELECT * FROM Student WHERE NAME = 'Matt'
````
 
 So it lends itself well to being used by other technology. In this case, let's look at how it will interact with a website.

A website has a **front-end**, which is the thing that users see, and can have a **back-end** as well. If you want your web application to be able to handle, store, and process data long term, you may set up a database to store the data.

There needs to be an established connection between the front-end and the back-end to allow this interaction to take place. So the website code will have some kind of connection string to log into the database and access the data records. Then, the website can update, change, delete, and modify data records by offering the user certain options.

The key here is that the wbesite should have a clean, defined list of interactions that the user can perform on the back-end database. And in every case, the website should be acting on behalf of the user to view, update, and delete data records.

But, of course, that's not always the case.

## The Exploit
Consider the following HTML/PHP code that sets up a back-end connection and performs a simple website login:

````php
....
$servername = "webDB";
$username = "root";
$password = "rootpassword";
$dbname = "website";
$conn = new mysqli($servername, $username, $password, $dbname);
````
````html
<p>Please fill in your credentials to login.</p>
<form action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]); ?>" method="post">
    <div class="form-group <?php echo (!empty($username_err)) ? 'has-error' : ''; ?>">
        <label>Username</label>
        <input type="text" name="username" class="form-control" value="<?php echo $username; ?>">
        <span class="help-block"><?php echo $username_err; ?></span>
    </div>

    <div class="form-group <?php echo (!empty($password_err)) ? 'has-error' : ''; ?>">
        <label>Password</label>
        <input type="password" name="password" class="form-control">
        <span class="help-block"><?php echo $password_err; ?></span>
    </div>
    <div class="form-group">
    <input type="submit" class="btn btn-primary" value="Login">
    </div>
</form>
````
````php
....

$sql = "SELECT id, name FROM users WHERE username = '$username' && password = '$password'";
...

// Check if username exists, if yes then verify password
if(mysqli_stmt_num_rows($stmt) >= 1) {
    mysqli_stmt_bind_result($stmt, $id, $name);

//fetch results from query and put into session
if(mysqli_stmt_fetch($stmt)) {
    $_SESSION["loggedin"] = true;
    $_SESSION["id"] = $id;
    $_SESSION["username"] = $username;
    $_SESSION["name"] = $name;
````

When rendered, this code works together to make a front-end login form and pull login credentail information from the back-end database.

The key to this interaction is in the SQL statement, which can be translated to "If the username and password of this field are set into a database query, and the query returns more than one row, the user must have a valid account. So, sign in the user that is returned by this query."

Now, think for a moment how you could break this. A few things come to mind:
 -  The SQL statement is looking for a query that returns one or more rows. If a user supplies a value that returns more than one row of input, the logic of the code says that user should be logged in. The code checks the username and password to validate it...right?
 - Not quite. The field on the front-end is inserting the username and password values into the SQL statement as variables. We can do a wonderful little trick here by inserting this into the login form:
 
![sqli1](../img/sqli1.png)
 

 turning this...

 ````sql
SELECT id, name FROM users WHERE username = '$username' && password = '$password'
````
...into this:
````sql
SELECT id, name FROM users WHERE username = 'admin' OR 1=1 -- - && password = '$password'
````

Let's break this down:
- `admin'`: inserting a username here that you want to inject into and log in as. Notice that the trailing single quote is **closing out the SQL query early** by wrapping the `admin` query up.
- `OR 1=1`: remember that the query is looking for specific criteria. We can split this query into a logical choice by either looking for an admin user OR validating another condition. And in this case (the classic boolean injection method), we're telling the SQL query to evaluate if 1=1. And, believe me, it does.
- `-- -`: notice the dash marks at the end. This is a SQL comment and it negates the values of everything else on the line. You can even see that the remainder of the query is greyed out in the code block above. It is no longer part of the query itself!

So when the query goes to the database, it is effectively asking "Hey, either show me the admin user's record OR evaluate if 1=1 is true! And if 1=1 is true, this query is valid. Therefore, log this user in!"

Again, computers do exactly what you tell them to do. ¯\\_(ツ)\_/¯

## The Types
The above example is a boolean injection, meaning it evaluates the actual query OR an additional query that you inject. This is the classic type of SQL injection, but is less common these days. Web app developers are getting better at identifying this and mitigating it. But SQLi is far from fixed.

Anywhere a query string can be manipulated without input checking, SQLi can be achieved. Some other methods of SQL injection include:

- Blind (Inferential) SQLi: often, a web application will not produce errors when SQLi is attempted. The adversary has to submit input and view the behavior or the page to prove the existence of an SQLi point. The SLEEP() test is a fantastic way to see if a user input SQL query can be executed by the database:
````php
admin' AND (SELECT 5834 FROM (SELECT(SLEEP(5)))ciEY)-- wtEo
````
...which makes the query evaluate the statement and perform the SLEEP function, which will hang the page for a given amount of seconds. If you inject this into the page and the page reliably hangs for however many seconds you provided, the page is vulnerable to SQLi. Be sure to modulate the number of seconds to check for a false-positive.

- UNION SELECT Injection: once an injection point has been found, the adversary can make use of the UNION SELECT feature of SQL to join different tables together and enumerate the entire back-end database. First, the number of columns in the database tables can be enumerated by building out a UNION SELECT query with an increasing number of NULL values. Once the number of columns is enumerated, an error will inform the user that the number of values in the columns must be equal to the number of columns. These sstatements may look like this:
````sql
admin' UNION SELECT NULL--
admin' UNION SELECT NULL,NULL--
admin' UNION SELECT NULL,NULL,NULL--
````
Then, the type of data stored in each column can be enumerated by sending specific data types into queries and viewing the results:
````sql
admin' UNION SELECT 'a',NULL,NULL,NULL--
admin' UNION SELECT NULL,'a',NULL,NULL--
admin' UNION SELECT NULL,NULL,'a',NULL--
admin' UNION SELECT NULL,NULL,NULL,'a'--
````
With these data points, the adversary can now begin to find interesting data rows. Assuming there are four columns and the first two contain text values, one can make the inference that there may be a username and password stored here. Then, it's a matter of injecting different values until one is hit.

This can be an arduous task. Luckily (or unluckily, depending on how you look at it), SQLmap can perform all of these enumerations and attacks automatically.

## The Damage
As far as web application attacks is concerned, SQLi is one of the most dangerous. An adversary can, in the right circumstances, get everything from credential disclosure to full-blown remote code execution. Once an SQL injection point is identified, there are few things that can stop an adversary from at least enumerating the back-end database. one of the best tools to deter this is a Web Application Firewall, which can examine the HTTP/S requests hitting the page and identify if web parameters are being injected into.