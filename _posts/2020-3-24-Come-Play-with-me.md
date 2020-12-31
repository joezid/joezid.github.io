---
layout: post
title: Come Play with me
categories: [CTF-Writeups]
tags: [Reverse Engineering,Competitions,CyberTalents]
description: Writeup for Come Play with me challenge from CyberTalents.
---
# Challenge name
Come Play with me
# Category
Malware Reverse Engineering
# Level
Hard
# Points
200
# Description
This strange game can give you the flag, will you play ?
# Solution

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/main/Images/comeplaywithme/1.png)

As we can see we are given x64 elf file ,so let's run the executable and see what it does.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/main/Images/comeplaywithme/2.png)

As we can see there is an error due to wrong key legnth ,so it's clear now that we are working with a frozen python executable.
Our goal now is to retrive the source code of the executable first thing we have to do is installing the pyinstaller library.

{% highlight yaml %}
pip3 install pyinstaller
{% endhighlight %}

We will use pyi-archive_viewer command to view the archivefiles in the executable.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/main/Images/comeplaywithme/3.png)

Let's exract the compiled main.py.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/main/Images/comeplaywithme/4.png)

Now we have the compiled version of the main.py so we have to decompile it retrive the source code.
We will use uncompyle6 to decompile the main and can be easily install using.

{% highlight yaml %}
pip3 install uncompyle6
{% endhighlight %}

Let's decompile the main.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/main/Images/comeplaywithme/5.png)

As we can see we got an error due to invalid magic number,To fix this error we will add 8 bytes to the header of the file the first 4 bytes is the magic number for the python 2.7 version and 4 bytes for time stamp (we can set it as zero).
**Note** we have to add these bytes in the little endian format.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/main/Images/comeplaywithme/6.png)

So let's save the file and try again.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/main/Images/comeplaywithme/7.png)

Now Let's reverse the source code to see how to find the flag.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/main/Images/comeplaywithme/8.png)
![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/main/Images/comeplaywithme/9.png)

Let's understand what does this script do, as we can see there is a decoding function whiche decode these this weird looking strings firstly it asks the user to enter his name the calculate the sum of the chars then taking the mod of 225 and then add 95 and this number will be used as the key for the decryption.
As we can see at the end of the script the flag is encrypted multiple time so we have to bruteforce the valid key,but first we have to decode the encrypted flag.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/main/Images/comeplaywithme/10.png)

Now we have the base64 of the encrypted flag let's make a small script to brute force the key.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/main/Images/comeplaywithme/11.png)

runing the script we find our flag.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/main/Images/comeplaywithme/12.png)

# Flag

**flag{tHe_fInAl_PyTHoN_ChaLleNGe}**


