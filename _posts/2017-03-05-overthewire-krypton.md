---
layout: post
title: "OverTheWire - Krypton Levels 0 - 3"
date: 2017-03-05
description: "Walk-through guide to OverTheWire Krypton."
number: "0x0f"
---
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

This is my write-up of the [OverTheWire](http://overthewire.org) wargame [Krypton](http://overthewire.org/wargames/krypton/). If you notice any problems please [contact](/contact) me to let me know.
 
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 0 ]

The first level requires us to decode a string from base64 to find the password to the first level.

I simply put the string into an online translator and moved on to the next level.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 1 ]

We navigate to the `/krypton/` directory to begin this level.

{% highlight shell %}
$ cd /krypton
$ ls -a
. .. README krypton2
{% endhighlight %}

The `README` file describes the file `krypton2` as a [ROT13](https://en.wikipedia.org/wiki/ROT13) cipher.

{% highlight shell %}
$ cat krypton2
YRIRY GJB CNFFJBEQ EBGGRA
{% endhighlight %}

In [Hacking Secret Ciphers with Python](https://inventwithpython.com/hackingciphers.pdf) I wrote a Caesar Cipher script that I will use to decode the message.

{% highlight python linenos %}
#!/usr/bin/python3.4
# Caesar Cipher
# http://inventwithpython.com/hacking (BSD Licensed)

import pyperclip

# the string to be encrypted/decrypted
message = input('Enter message: ')

# the encryption/decyrption key
key = int(input('Enter key: '))

# set program to encrypt or decrypt
while True:
    setMode = input('Enter mode (encrypt/decrypt): ')
    
    if setMode == 'encrypt':
        mode = 'encrypt'
        break
    elif setMode == 'decrypt':
        mode = 'decrypt'
        break
    else:
        print('Invalid input.')

# all possible symbols that can be encrypted        
LETTERS = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'

# stores the encrypted/decrypted form of the message
translated = ''

# capitalise the string in the message
message = message.upper()

# run the encryption/decryption code on each symbol in the message string
for symbol in message:
    if symbol in LETTERS:
        # get the (en/de)crypted number for this symbol
        num = LETTERS.find(symbol) # get the number of the symbol
        if mode == 'encrypt':
            num = num + key
        elif mode == 'decrypt':
            num = num - key
            
        # handle the wrap around if num is larger than len of LETTERS or less than 0
        if num >= len(LETTERS):
            num = num - len(LETTERS)
        elif num < 0:
            num = num + len(LETTERS)
            
        # add (en/de)crypted number's symbol at the end of translated
        translated = translated + LETTERS[num]
        
    else:
        # just add the symbol without (en/de)crypting
        translated = translated + symbol
        
print(translated)

# copy to clipboard
pyperclip.copy(translated)
{% endhighlight %}

We can run it and add 13 as the key, as it is a ROT13 cipher.

{% highlight shell %}
$ python3 caesarCipher.py
Enter message: YRIRY GJB CNFFJBEQ EBGGRA
Enter key: 13
Enter mode (encrypt/decrypt): decrypt
LEVEL TWO PASSWORD ***********
{% endhighlight %}

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 2 ]

Another Caesar Cipher, this level includes an `encrypt` script that will encrypt our plaintext to help us reveal the key.

First we make a `tmp` directory to work in and include a link to the keyfile as is required in the instructions

{% highlight shell %}
$ mkdir /tmp/maxmunday
$ ln -s /krypton/krypton2/keyfile.dat
$ chmod 777 .
{% endhighlight %}

We then place some text into a file and encrypt it 

{% highlight shell %}
$ echo ABC > plaintext
$ /krypton/krypton2/encrypt plaintext
{% endhighlight %}

We can see the output in a newly created `ciphertext`

{% highlight shell %}
$ cat ciphertext
MNO
{% endhighlight %}

So we can assume the key is 12, as ABC shifted 12 places is MNO. So we can run our previous script with the key of 12

{% highlight shell %}
$ python3 caesarCipher.py
Enter message: OMQEMDUEQMEK
Enter key: 12
Enter mode (encrypt/decrypt): decrypt
***********
{% endhighlight %}

And there is the password.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ Level 3 ]

For this level we are given two hints along with some intercepted encrypted messages.

{% highlight shell %}
$ cat HINT1
Some letters are more prevalent in English than others.
$ cat HINT2
"Frequency Analysis" is your friend.
{% endhighlight %}

When we check out the first message we see a large cipher text block.

{% highlight shell %}
$ cat found1
CGZNL YJBEN QYDLQ ZQSUQ NZCYD SNQVU BFGBK GQUQZ QSUQN UZCYD SNJDS UDCXJ ZCYDS NZQSU QNUZB WSBNZ QSUQN
UDCXJ CUBGS BXJDS UCTYV SUJQG WTBUJ KCWSV LFGBK GSGZN LYJCB GJSZD GCHMS UCJCU QJLYS BXUMA UJCJM JCBGZ
CYDSN CGKDC ZDSQZ DVSJJ SNCGJ DSYVQ CGJSO JCUNS YVQZS WALQV SJJSN UBTSX COSWG MTASN BXYBU CJCBG UWBKG 
JDSQV YDQAS JXBNS OQTYV SKCJD QUDCX JBXQK BMVWA SNSYV QZSWA LWAKB MVWAS ZBTSS QGWUB BGJDS TSJDB WCUGQ 
TSWQX JSNRM VCMUZ QSUQN KDBMU SWCJJ BZBTT MGCZQ JSKCJ DDCUE SGSNQ VUJDS SGZNL YJCBG UJSYY SNXBN TSWAL 
QZQSU QNZCY DSNCU BXJSG CGZBN YBNQJ SWQUY QNJBX TBNSZ BTYVS OUZDS TSUUM ZDQUJ DSICE SGNSZ CYDSN QGWUJ 
CVVDQ UTBWS NGQYY VCZQJ CBGCG JDSNB JULUJ STQUK CJDQV VUCGE VSQVY DQASJ UMAUJ CJMJC BGZCY DSNUJ DSZQS 
UQNZC YDSNC USQUC VLANB FSGQG WCGYN QZJCZ SBXXS NUSUU SGJCQ VVLGB ZBTTM GCZQJ CBGUS ZMNCJ LUDQF SUYSQ 
NSYNB WMZSW TBUJB XDCUF GBKGK BNFAS JKSSG QGWDC USQNV LYVQL UKSNS TQCGV LZBTS WCSUQ GWDCU JBNCS UESGN 
SUDSN QCUSW JBJDS YSQFB XUBYD CUJCZ QJCBG QGWQN JCUJN LALJD SSGWB XJDSU COJSS GJDZS GJMNL GSOJD SKNBJ 
STQCG VLJNQ ESWCS UMGJC VQABM JCGZV MWCGE DQTVS JFCGE VSQNQ GWTQZ ASJDZ BGUCW SNSWU BTSBX JDSXC GSUJS 
OQTYV SUCGJ DSSGE VCUDV QGEMQ ESCGD CUVQU JYDQU SDSKN BJSJN QECZB TSWCS UQVUB FGBKG QUNBT QGZSU QGWZB 
VVQAB NQJSW KCJDB JDSNY VQLKN CEDJU TQGLB XDCUY VQLUK SNSYM AVCUD SWCGS WCJCB GUBXI QNLCG EHMQV CJLQG 
WQZZM NQZLW MNCGE DCUVC XSJCT SQGWC GJKBB XDCUX BNTSN JDSQJ NCZQV ZBVVS QEMSU YMAVC UDSWJ DSXCN UJXBV 
CBQZB VVSZJ SWSWC JCBGB XDCUW NQTQJ CZKBN FUJDQ JCGZV MWSWQ VVAMJ JKBBX JDSYV QLUGB KNSZB EGCUS WQUUD 
QFSUY SQNSU
{% endhighlight %}

The other messages contain similar text blocks.

We know that these messages are in English, and are all encrypted with the same key. So we can do some frequency analysis on this messages.

I used a frequency analysis tool found [here](http://www.dcode.fr/frequency-analysis) and inputed the text from the messages.

Using a list of most frequently used letters, bigrams, and trigrams found [here](http://www.counton.org/explorer/codebreaking/frequency-analysis.php), I slowly worked through the anaylsis and applied this to the `krypton4` text as it was much more managable.

{% highlight shell %}
$ cat krypton4
KSVVW BGSJD SVSIS VXBMN YQUUK BNWCU ANMJS
{% endhighlight %}

Firstly, the most common three-letter word in English is 'THE'. In the analysis it shows the most common trigram as 'JDS'. 

Similarly, the letter 'e' is the most common, and the letter 'S' is the most common in the messages. 

So we can assume

{% highlight shell %}
J == T
D == H
S == E
{% endhighlight %}

Working through the most common letters we find:

{% highlight shell %}
Q == A
B == O
{% endhighlight %}

Working through the most common double letters we find 

{% highlight shell %}
U == S
V == L
{% endhighlight %} 

Applying these letters one by one begins to reveal the `krypton4` message

{% highlight shell %}
KellWoGetheleIelXoMNYassKoNWCsANMte
{% endhighlight %}

I continued this form of analysis letter by letter until the message was revealed

{% highlight shell %}
WELLDONETHELEVELFOURPASSWORDIS*****
{% endhighlight %}


\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-


\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-