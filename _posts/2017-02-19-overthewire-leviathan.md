---
layout: post
title: "OverTheWire - Leviathan Levels 0 - 7"
date: 2017-02-19
description: "Walk-through guide to OverTheWire Leviathon."
number: "0x0d"
---
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

This is my write-up of the [OverTheWire](http://overthewire.org) wargame [Leviathan](http://overthewire.org/wargames/leviathan/). If you notice any problems please [contact](/contact) me to let me know.
 
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 0 ]

The only direction we get in this wargame is that data can be found in the home directories.

So, after we SSH in to `leviathan0@leviathan.labs.overthewire.org`, we can search the home directory

{% highlight shell %}
$ ls -a
{% endhighlight %}

We see

{% highlight shell %}
.  ..  .backup  .bash_logout  .bashrc  .profile
{% endhighlight %}

We will check the `.backup` directory

{% highlight shell %}
$ ls ./.backup
{% endhighlight %}

And we see

{% highlight shell %}
bookmarks.html
{% endhighlight %}

Opening the file with `cat` revealed a large amount of text so we can narrow it down with `grep`

{% highlight shell %}
$ cd ./.backup
$ grep leviathan1 bookmarks.html
{% endhighlight %}

And it outputs the password in the terminal.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 1 ]

When we list the contents of the home directory we see an executable file named `check`. When we run it, it asks for a password

{% highlight shell %}
$ ./check
password: 'password'
Wrong password, Good Bye ...
{% endhighlight %}

So it seems it is testing the input against a stored string. We can run `ltracer` to see the library calls the script makes.

{% highlight shell %}
$ ltrace ./check
__libc_start_main(0x804852d, 1, 0xffffd7b4, 0x80485f0 <unfinished ...>
printf("password: ")                                     = 10
getchar(0x8048680, 47, 0x804a000, 0x8048642password: 
)             = 10
getchar(0x8048680, 47, 0x804a000, 0x8048642
)             = 10
getchar(0x8048680, 47, 0x804a000, 0x8048642
)             = 10
strcmp("\n\n\n", "sex")                                  = -1
puts("Wrong password, Good Bye ..."Wrong password, Good Bye ...
)                     = 29
+++ exited (status 0) +++
{% endhighlight %}

Checking the man page for `strcmp` we can see that it is a C function that compares strings. Therefore, we can assume 'sex' is the password.

{% highlight shell %}
$ ./check
password: sex
$ cat /etc/leviathan_pass/leviathan2
{% endhighlight %}

And we have the password for the next level.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 2 ]

When we list the contents of the home directory we see an executable file named `printfile`.

{% highlight shell %}
$ ./printfile
*** File Printer ***
Usage: ./printfile filename
{% endhighlight %}

Attempting to get into `/etc/leviathan_pass/leviathan3` gives us

{% highlight shell %}
$ ./printfile /etc/leviathan_pass/leviathan3
You cant have that file...
{% endhighlight %}

Running `ltrace` on it reveals limited information

{% highlight shell %}
$ ltrace ./printfile /etc/leviathan_pass/leviathan3
__libc_start_main(0x804852d, 2, 0xffffd784, 0x8048600 <unfinished ...>
access("/etc/leviathan_pass/leviathan3", 4)              = -1
puts("You cant have that file..."You cant have that file...
)                       = 27
+++ exited (status 1) +++
{% endhighlight %}

We need a file that isn't disallowed so we can see where it goes after the `access()` call. It is probably best to make our own file for this purpose.

{% highlight shell %}
$ touch /tmp/maxmunday/file.txt
$ ltrace ./printfile /tmp/maxmunday/file.txt
__libc_start_main(0x804852d, 2, 0xffffd754, 0x8048600 <unfinished ...>
access("/tmp/maxmunday/file.txt", 4)                                                    = 0
snprintf("/bin/cat /tmp/maxmunday/file.txt"..., 511, "/bin/cat %s", "/tmp/maxmunday/file.txt") = 32
system("/bin/cat /tmp/maxmunday/file.txt"... <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                                                                  = 0
+++ exited (status 0) +++
{% endhighlight %}

The `access()` call and the call to `/bin/cat` seem to be a bit different. 

The quotes in the `system()` call lead me to believe that if you put two files as arguments to `printfile`, `access()` might read it as a single file, but `cat` will probably read it as two individual files. That might get around our file privilege problem.

I tried a few combinations until a single file with a space in the name offered some promise

{% highlight shell %}
$ touch /tmp/maxmunday/two\ files
$ ltrace ./printfile /tmp/maxmunday/two\ files 
__libc_start_main(0x804852d, 2, 0xffffd754, 0x8048600 <unfinished ...>
access("/tmp/maxmunday/two files", 4)                      = 0
snprintf("/bin/cat /tmp/maxmunday/two file"..., 511, "/bin/cat %s", "/tmp/maxmunday/two files") = 33
system("/bin/cat /tmp/maxmunday/two file".../bin/cat: /tmp/maxmunday/two: No such file or directory                                                                                             
/bin/cat: files: No such file or directory                                                      
 <no return ...>                                                                                
--- SIGCHLD (Child exited) ---                                                                  
<... system resumed> )                                     = 256                                
+++ exited (status 0) +++                                                    
{% endhighlight %}

Here we see that `/bin/cat` has tried to process the file `two files` as `two` and `files`, while `access()` has processed it as a single file.

Knowing this we just have to get the file `two` to print the contents of `/etc/leviathan_pass/leviathan3`. 

I had tried a symbolic link previously in an attempt to get two files to pass through `access()` but it hadn't worked. But with this file we should be able to do it correctly.

{% highlight shell %}
$ ln -s /etc/leviathan_pass/leviathan3 /tmp/maxmunday/two
$ ./printfile /tmp/maxmunday/two\ files
{% endhighlight %}

And it worked! It prints the password and a `/bin/cat: files: No such file or directory` to confirm to us that we were spot on.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 3 ]

Again in the home directory there is an executable. This one is called `level3`.

{% highlight shell %}
$ ./level3
Enter the password> 'password'
bzzzzzzzzap. WRONG
{% endhighlight %}

We run `ltrace` on it 

{% highlight shell %}
$ ltrace ./level3
__libc_start_main(0x80485fe, 1, 0xffffd794, 0x80486d0 <unfinished ...>
strcmp("h0no33", "kakaka")                                 = -1
printf("Enter the password> ")                             = 20
fgets(Enter the password> 
"\n", 256, 0xf7fcac20)                               = 0xffffd58c
strcmp("\n", "snlprintf\n")                                = -1
puts("bzzzzzzzzap. WRONG"bzzzzzzzzap. WRONG
)                                 = 19
+++ exited (status 0) +++
{% endhighlight %}

Now I must admit I puzzled over the "h0no33" and "kakaka" strings inputing them into the password field and wondering what I was gettting wrong before noticing the second `strcmp()` call further down...

{% highlight shell %}
$ ./level3 
Enter the password> 'snlprintf'
[Youve got shell]!
$ cat /etc/leviathan_pass/leviathan4
{% endhighlight %}

And there we have it.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 4 ]

Listing the files in the home directory we see the directory `.trash` that contains the executable `bin`.

{% highlight shell %}
$ ./.trash/bin
01010100 01101001 01110100 01101000 00110100 01100011 01101111 01101011 01100101 01101001 00001010 
{% endhighlight %}

Seems like binary, I ran it through an online translator and the password is revealed.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 5 ]

The executable is this level is called `leviathan5`. Let's run it

{% highlight shell %}
$ ./leviathan5 
Cannot find /tmp/file.log
{% endhighlight %}

We will run an `ltrace` on it

{% highlight shell %}
$ ltrace ./leviathan5 
__libc_start_main(0x80485ed, 1, 0xffffd7a4, 0x8048690 <unfinished ...>
fopen("/tmp/file.log", "r")                                = 0
puts("Cannot find /tmp/file.log"Cannot find /tmp/file.log
)                          = 26
exit(-1 <no return ...>
+++ exited (status 255) +++
{% endhighlight %}

We'll try adding a symbolic link to the password file from the `/tmp/file.log` file

{% highlight shell %}
$ ln -s /etc/leviathan_pass/leviathan6 /tmp/file.log
$ ./leviathan5
{% endhighlight %}

The password prints to the terminal.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 6 ]

For this level we run the executable we have come to expect

{% highlight shell %}
$ ./leviathan6 
usage: ./leviathan6 <4 digit code>
$ ./leviathan6 1111
Wrong
{% endhighlight %}

So looks like we need to brute force a PIN. I modified a simple bash script from 'Bandit' to work here

{% highlight shell %}
$ nano /tmp/maxmunday/pin.sh
{% endhighlight %}

{% highlight shell linenos %}
#!/bin/bash

START=0000
LAST_PIN=9999

for i in `seq $START $LAST_PIN`;
do
        ~/leviathan6 $i
done
{% endhighlight %}

We then give it the appropriate permissions and run it

{% highlight shell %}
$ chmod 777 /tmp/maxmunday/pin.sh
$ /tmp/maxmunday/pin.sh
{% endhighlight %}

After the word 'Wrong' prints 10,000 times in the terminal, we get a shell open up

{% highlight shell %}
$ cat /etc/leviathan_pass/leviathan7
{% endhighlight %}

And that is Leviathan.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-


\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-