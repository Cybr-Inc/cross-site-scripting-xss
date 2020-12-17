# XSS Concepts
Let’s say that you have a static website that’s made of only HTML and CSS. When you visit the website, the browser renders that HTML and CSS and that’s what you see on your screen…but nowadays, most websites also include dynamic elements…

For example, data gets pulled from a database, or it asks for user input and uses that user input to do something. 

So if we visualize that, you might see an input form, where the user submits information, and the application sends that information to another page via the URL. That other page grabs the URL, and parses the information in it to grab what the user submitted. 

Let’s say that this information is used to populate an HTML field on the page — in which case it grabs that data from the URL, parses it, and then outputs it in the HTML of the page, essentially allowing the user to modify that page. 

If we put our web developer hat on for a minute, and we pretend that we have direct access to modify the code on this page, we could say: “you know what, I need to add a script on this page in order to enhance functionality.” Usually, to follow best practices, we’d either add the script in the header or footer of the page, but technically, the script can be added anywhere in this page. In fact, we could add our script right here, and the browser would load it when the web page gets rendered.

This means that, under the right circumstances, an attacker can abuse that to send a script payload via this vulnerable input, and then modify the application to do something that it wasn’t intended to.

In other words, the attacker could inject a malicious script which would then get loaded by the browser, since the browser thinks that it’s part of the web page and that it needs to load the script for the web page to function properly.

Enter Cross-Site Scripting…

# What is Cross-Site Scripting (XSS)?
Cross-Site Scripting, aka XSS, is a type of injection attack where malicious scripts are injected in trusted websites, and executed by the visitor’s browser, just like we talked about.

Basically, imagine a simple, supposedly safe website with users, and an attacker finds a way to exploit that website in order to send malicious code to one or more of those users.

That malicious code is generally in the form of browser-side scripts, so nothing fancy or crazy. Literally what everyone in the world with internet has access to create.

An attacker finds a way to take their malicious code and infect the HTML document without causing the web server itself to be infected. Instead, it uses the server as a vector to present malicious content back to the user, either instantly from the request (which is called a reflected attack), or delayed through storage and retrieval (which is called a stored or persistent attack).

But again and to clarify, even when I say that the malicious code is stored and retrieved at a later time, I don’t mean that the server itself is infected with malicious code. The malicious code only gets executed through the user’s browser when that user visits a web page that fetches the stored code. So the core application remains unaltered, but it can be made to serve malicious content to clients.

With a third type, DOM-based, the payload technically never even has to reach the servers at all.

So then when is Cross-Site Scripting possible? What causes the vulnerability, and how do people exploit that vulnerability?

# When is XSS possible?
Like other types of web-based injection attacks, Cross-Site Scripting attacks are possible when an application uses input from a user within the output that it generates without properly validating or encoding that input.

If you’re not familiar with the term encoding, it simply means converting data or a sequence of characters into a specified format to securely process data. Validating refers to verifying that the data is what you expected. We will definitely be talking a lot more about encoding and validating in the defense section of this course, but I wanted to make sure you weren’t confused by those terms.

In any case, since we were talking about the application grabbing input from a user and then outputting the information, the page is only reflecting back what was submitted in a request, but the content of that request might have characters that break out of the ordinary and expected text content, and instead introduce HTML or JavaScript content that the developer did not intend for, and that the application did not expect.

- Expected input: `userName`
- Expected output: `Hello, userName!`
- Unexpected input: `Bob<script>alert(1)</script>`
- Unexpected output: `Bob with a pop-up box saying 1`

The good news is that modern browsers are getting smarter and smarter, and they now include a lot of defenses out of the box to prevent successful XSS attacks.

The bad news is that these browser security controls don’t prevent all XSS attacks, and while they’re definitely not as common as they used to be, they’re still found in the real-world, and they are one of the most commonly attempted attacks on applications. In fact, they are listed as the second most prevalent issue in the OWASP Top 10 with an Exploitability, Prevalence, and Detectability rating of 3, and a Technical impact rating of 2, since some of the XSS attacks will have much less severe impacts than others.

XSS Impact
Successful exploitation of XSS can do a lot of damage. If an attacker is able to inject malicious scripts into a web application, then the browser may end up believing that the script comes from a trusted source, and it will let that script access cookies (document.cookie), session tokens, or other sensitive information stored in the browser and used with the site to:
1. Grant login access or steal credentials
2. Authorize unwanted payments
3. Redirect users to a look-alike website
4. and more

Some scripts could even potentially re-write the HTML on the page, changing the appearance of the website, causing you to perform actions you may not otherwise take, or sending your private information to another server without you even realizing it. For example, an attacker could insert a fake login form into the existing page, set the form’s action attribute to target their own server, and therefore trick the user into submitting sensitive information without even realizing it.

Apart from information we’ve already talked about, an attacker could also register and send a user’s keystrokes to their own server, by adding what’s called an event listener (addEventListener) potentially recording sensitive information like passwords and credit card numbers.

# XSS Examples
Let’s take a look at some example basic XSS attack scripts so that we can illustrate the concepts we’ve been talking about.

One of the most common examples you will see, and also frankly one of the least realistic or practical examples, is the alert() injection:

`<script src=javascript:alert("xss")>`
or

`<script>alert("xss")</script>`
And this alert injection could happen via a URL, like this:

`https://example.com/search?=%22%3Cscript%20src%20%3Djavascript%3Aalert%28%29%3E`
Or it could be injected in vulnerable input fields.

All this would do is create one of those annoying alert boxes that just writes out “`xss`”, but this is meant as a proof of concept to show that there is a vulnerability. There is very little practical usage otherwise. So while we will be using this payload throughout the course to find vulnerabilities, we’ll also look at more practical payloads.

Alright, now that we’ve reviewed concepts of XSS, let’s wrap up this lesson and let’s move on to the next where we explore the 3 main types of XSS attacks, how they work, the impact they can have, and how they can be exploited.

Go ahead and mark this lesson as complete, and I’ll see you in the next!
