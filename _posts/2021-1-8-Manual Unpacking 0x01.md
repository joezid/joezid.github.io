---
layout: post
title:  +5000 Rank CTF 2020
categories: [CTF-Writeups]
tags: [Reverse Engineering,Competitions]
description: Writeup for the reverse engineering challenges from  +5000 Rank CTF 2020 from cybertalents.
---
# Challenge name
Sky Fall
# Category
Reverse Engineering
# Level
Meduim
# Points
100
# Description
Reversing a unity game.
# Solution
As usual when reversing a unity game you have to start the game and start digging around.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/1.PNG?raw=true)

As we can see it's a driving game so let's move around.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/2.PNG?raw=true)

Moving around we can natoice this weird looking cube.
So let's search for the Assembly-CSharp.dll and open it in dnspy.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/3.PNG?raw=true)

As we can there is two function that we are interested in flagfall and cube.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/4.PNG?raw=true)

Checking the cube function we can notice that when we arrive to the cube nothing will happen as we see because the condtion will never be true on the OnTriggerEnter function.
So i changed the condtion to be like this.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/5.PNG?raw=true)

Testing the game again we will found that when we arrive to the cube we moved to the scene number 2.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/6.PNG?raw=true)

As we can see there is a timer and when the time finshes we will see flagfall;(.
So let's check flagfall function.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/7.PNG?raw=true)

To fix this let's remove the flagfall part from flagfall method.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/8.PNG?raw=true)

Runing the game again we find our flag.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/+5000proctf/9.png?raw=true)

# FLAG
FLAg{EJeCTO_S3470_CUZ!}


# Challenge name
frenzy
# Category
Reverse Engineering
# Level
Meduim
# Points
100
# Solution

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/1_1.PNG?raw=true)

As we can see we are given a x64 staticlly linked ELF file.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/1_2.PNG?raw=true)

Runing the script we will notica that we have pass a file as the first argument to the executable,passing any file to the elf we will see this result.
So let's open ida and start work.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/1_3.PNG?raw=true)

Checking the main as we see above we can see there over 10k of assembly instructions in the main only.
So let's understnad what it does.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/1_4.PNG?raw=true)

The binary strat by checking the argc if it <2 print the usage then he start by reading  1024 byte from the given file save these bytes at &unk_4D3B60.
Then start by taking 1,2 or 3 bytes from specfic offset then hash these bytes using one of md5,sha1,sha256 or sha512 then cmp the generated hash with hashes in memory.
This operation repeated 496 time with different number of bytes and diffrenet offset, as we see the problem here that the file has to be 1024 byter so there will a lot of garpage.
So after some time i found a tricky solution all i have to do is extracting the saved hashes from memory which was 360 hashes and i start cracking them online searching for the open
curly bracket '{'.
After 10 mins i found that this sha512 hash '95ffe6e88980cc0a218d2ae5bd0d2ba0e231c76866412da188bdb4a062d43e0d1d855f2a73747fd94fbf621d1901c4114a0b5fe230dccb3a03057fc0e261e9f3' is the hash of '	g{b'.
So let's search for this hash as text in ida.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/1_5.PNG?raw=true)

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/1_6.PNG?raw=true)

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/1_7.PNG?raw=true)

Now we are back in the main and we have the offset where the flag should start &unk_4D3DF1-3 but because we know the first three chars of flag we can neglect it and start from this offset.
To find the next hash as we know at address &unk_4D3DF1 three bytes are being hashed so we will add 3 to the end of junk hex number to be &unk_D3DF4.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/1_8.PNG?raw=true)

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/1_9.PNG?raw=true)

Now we have the hash of the next 1 char 'e1e1d3d40573127e9ee0480caf1283d6' cracking it 'R'.
After repeating the same steps again and again we will reveal our flag.
Note all the hashes i cracked them online but the last hash i didn't find it online so i make a small script to bruteforce it.

![Image](https://github.com/joezid/joezid.github.io/blob/main/Images/%2B5000proctf/1_10.PNG?raw=true)

# FLAG

flag{bRuT3_f0rC3_1s_aN_3asY_s0lution}



