---
layout: post
title: "OverTheWire - Bandit Levels 0 - 15"
date: 2016-11-10
description: "Walk-through guide to OverTheWire Bandit."
number: "0x08"
---
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

This is my write-up of the [OverTheWire](http://overthewire.org) wargame [Bandit](http://overthewire.org/wargames/bandit/). This is a Bash command line wargame aimed at newcomers. Although I have done this before, I recently ran through it again with the [Swinburne University Cyber Security Club](http://scsc.wtf/) and thought it would be a good idea to do a write-up. If you notice any problems please [contact](/contact) me to let me know.
 
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 0 ]

We start by using SSH to log into `bandit.labs.overthewire.org`. We can use the command `ls` to list the files in the current directory, which displays the file `readme`. We then can use 

{% highlight shell %}
$ cat readme
{% endhighlight %}

to display the contents of the file in the command line. This is the password to the next level.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 1 ]

After using `ls` to find the file titled `-`, we can use the command 

{% highlight shell %}
$ cat ./-
{% endhighlight %}

to display the contents. `./` means you are looking in the current directory.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 2 ]

The file is this directory has spaces that need to be escaped to work in the command line. For me, I simply type `cat s` then the tab auto-complete finishes the command to 

{% highlight shell %}
$ cat spaces\ in\ this\ filename
{% endhighlight %}

The `\` tells the command line to ignore the character following it, which in this case is a space.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 3 ]

This directory contains the subdirectory `inhere`. We use 

{% highlight shell %}
$ cd inhere/
{% endhighlight %}

to change the working directory to `inhere`, then we use 

{% highlight shell %}
$ ls -a
{% endhighlight %}

to list all the files, including the hidden ones. We see a file called `.hidden`, so we use 

{% highlight shell %}
$ cat ./.hidden
{% endhighlight %}

to display its contents.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 4 ]

There are several files contained within the directory `inhere` and the password is contained in the only human readable file. We can use 

{% highlight shell %}
$ file ./-*
{% endhighlight %}

to list the file types. File `-file07` reads `ASCII text` so we then 

{% highlight shell %}
$ cat ./-file07
{% endhighlight %}

to display the password.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 5 ]

The are again several directories with files in them, but the password is contained in a human readable file, with a size of 1033 bytes. We can use the command 

{% highlight shell %}
$ find ./ -size 1033c
{% endhighlight %}

and it displays `./maybehere07/.file2`, we can use `cat` to display the password.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 6 ]

The password is 'somewhere on the server' with the following attributes: - owned by user bandit7 - owned by group bandit6 - 33 bytes in size.

We can use the command 

{% highlight shell %}
$ find / -user bandit7 -group bandit6  -size 33c 2</dev/null 
{% endhighlight %}

and cat into the resulting file location 

{% highlight shell %}
$ cat /var/lib/dpkg/info/bandit7.password
{% endhighlight %}

The `2</dev/null` bins the errors and cleans up the resulting output.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 7 ]

The password is stored in `data.txt` next to the word 'millionth'.

We can use the command

{% highlight shell %}
$ cat data.txt | grep millionth
{% endhighlight %}

to display the password. `|` pipes the resulting output into the next command, `grep` then searches for the line of text next to the word 'millionth'.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 8 ]

The password is stored in `data.txt` and is the only line of text that occurs only once.

We can use the command

{% highlight shell %}
$ sort data.txt | uniq -u
{% endhighlight %}

This 'sorts' the lines of text in file so that that can be searched, and pipes the results into `uniq -u` that displays the only unique line of text.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 9 ]

The password is stored in `data.txt` in one of the few human-readable strings, beginning with several ‘=’ characters.

{% highlight shell %}
$ strings data.txt | grep ==
{% endhighlight %}

This uses `strings` which prints printable characters, then pipes it into `grep` which searches for the '==' characters.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 10 ]

The password is in the data.txt file, encoded in base64.

{% highlight shell %}
$ base64 -d data.txt
{% endhighlight %}

This simply decodes base64 encoded data and displays the password.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 11 ]

The password is in the data.txt file, encoded by a rot13 cipher, meaning all lowercase and uppercase letters have been rotated by 13 positions.

{% highlight shell %}
$ cat data.txt | tr '[A-Za-z]' '[N-ZA-Mn-za-m]'
{% endhighlight %}

gives us the password.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 12 ]

The data.txt file in this level is a repeatedly compressed hexdump, the following commands unpack the file, and display the password.

{% highlight shell %}
$ xxd -r data.txt data  
$ mv data data.gzip  
$ gunzip data.gzip  
$ mv data.gzip data.gz  
$ gunzip data.gz  
$ bunzip2 data  
$ mv data.out data.gz  
$ gunzip data.gz  
$ tar -xf data  
$ file data5.bin   
$ tar -xf data5.bin  
$ bunzip2 data6.bin  
$ mv data8.bin data8.bin.gz  
$ gunzip data8.bin.gz   
$ cat data8
{% endhighlight %}

Between each command `file data` can be used to determine which following command needs to be used.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 13 ]

The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level.

{% highlight shell %}
$ ssh -i sshkey.private bandit14@localhost
{% endhighlight %}

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 14 ]

The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

{% highlight shell %}
$ cat /etc/bandit_pass/bandit14
{% endhighlight %}

to display password (we have been told previously this is where it is contained). Then

{% highlight shell %}
$ telnet localhost 30000
{% endhighlight %}

and enter the password when prompted which will in turn display the next password before closing.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 15 ]

The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL encryption.

{% highlight shell %}
$ openssl s_client -connect localhost:30001
{% endhighlight %}

and enter the password when prompted which will in turn display the next password.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

[Levels 16 - 20 write-up](https://www.maxmunday.com/blog/2017/02/05/overthewire-bandit).

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-
