---
layout: post
title: "OverTheWire - Natas Write-Up Levels 0 - 10"
date: 2016-10-05
description: "Walk-through guide to OverTheWire Natas."
number: "0x07"
---
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

This is my write-up of the [OverTheWire](http://overthewire.org) wargame [Natas](http://overthewire.org/wargames/natas/) levels 0 - 10. This is an enjoyable serverside web-security wargame aimed at beginners. If you notice any problems please [contact](/contact) me to let me know.
 
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 0 ]

For each level we navigate to `http://natasX.natas.labs.overthewire.org`, where X is the level number. Similarly, the username is always `natasX`, with X also the level number.

We start by navigating to `http://natas0.natas.labs.overthewire.org`, and enter the credentials `natas0:natas0`. 

This takes us to a page stating:

`You can find the password for the next level on this page.`

I viewed the source and found the password for level 1.

Easy start.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 1 ]

We navigate to `http://natas1.natas.labs.overthewire.org` and authenticate using natas1 and the password we found on the last level.

Same description but says right-clicking is disabled.

I just typed `view-source:http://natas1.natas.labs.overthewire.org` into the browser and up comes the password.


\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 2 ]

We see this

`There is nothing on this page.`

I view the source again and find a reference to a file location.

`<img src="files/pixel.png">`

I navigate to `http://natas2.natas.labs.overthewire.org/files/` and see the file index. This shows another file `users.txt`. I go there and there we have the password.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 3 ]

Another page stating there is nothing there. The source reveals a clue though.

`<!-- No more information leaks!! Not even Google will find it this time... -->`

That alludes to the `robots.txt` file that tells webcrawlers what they can and can't access. So I navigate to http://natas3.natas.labs.overthewire.org/robots.txt and it displays a disallowed page, `/s3cr3t/`.

I navigate to there and see another file index that shows a `users.txt` file, in that we find the password.


\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 4 ]

An error comes up on the page

`Access disallowed... users should only come from http://natas5.natas.labs.overthewire.org/`

Using the free edition of [Burp Suite](https://portswigger.net/burp/) I use the proxy to intercept the page, I change the referer to `http://natas5.natas.labs.overthewire.org/`, and it prints the password on the page.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 5 ]

This time the error states

`Access disallowed. You are not logged in`

Again I use Burp to intercept the page. I see it has a cookie with a `loggedin = 0`. I change it to `loggedin = 1` and forward it on and the password prints on screen.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 6 ]

This level brings up a query box. They allow us to view the source which show a PHP `$_POST` code that tests for a secret and posts it if it matches.

It shows an include for a file path though. Navigating there shows nothing, but viewing the source of this file in burp gives us the secret. Inputing that into the query displays the password.


\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 7 ]

This gives us a page with a `Home` and an `About` links to other pages. The URL for `Home` is `http://natas7.natas.labs.overthewire.org/index.php?page=home`. I chuck in a few move back operators `/../` and navigate to `/etc/natas_webpass/natas8` (they state at the beginning all passwords are held in this file), and the password was displayed.

![](/pictures/hackerman.jpg){:height="360px" width="640px"}

This is fun.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 8 ]

Another PHP form with another secret. This time the secret has been encoded with `bin2hex(strrev(base64_encode($secret)));`.

So to decode the secret we simply need to reverse the process.

Time to hit the [PHP docs](https://secure.php.net/docs.php)...

I ran PHP interactively in the terminal and used the following code to decode the secret

`echo base64_decode(strrev(hex2bin('###############')));`

It outputted a short string, I submited that in the query and it gave me the password.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 9 ]

This page shows a search query and lets us view the source code. Viewing that shows us how the page uses a varible to search a dictionary to output similar words. It shows no filtering though, so it should be vulnerable to command execution.

I tried a few little strings before one popped it.

`-; cat /etc/natas_webpass/natas10`

Running this query outputs the password.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 10 ]

Another search with command execution it seems, only this one filters out the characters `;` `|` `&`.

This one took me a while actually. I stuffed around with all sorts of characters before essentially stumbling to the following string by accident

`"" /etc/natas_webpass/natas11`

But it worked...

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

Levels 10 - 20 to follow soon.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-
