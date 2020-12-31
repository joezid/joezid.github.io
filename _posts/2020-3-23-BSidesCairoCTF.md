---
layout: post
title: BSidesCairoCTF2020
categories: [CTF-Writeups]
tags: [Reverse Engineering,Competitions]
description: Writeup for the reverse engineering challenge from BSidesCairoCTF.
---
# Challenge name
Homework
# Category
Reverse Engineering
# Points
400
# Description
We are given three files and our goal is to retrive the homework.txt file.
# Solution

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/1.png)

As we can see we are given three file encryptor,decryptor and core dump file for the decryptor.
Our plan now is checking the two elf files (encryptor & decryptor) to see the encryption algrothim and see how we can retrive the encrypted data.
It's time for ida after opening the homeworkcrypter.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/2.png)

As we can see it start by reading homework.txt data then read the first 64 bytes from the elf file and do some encoding to the the extracted data from elf in the function sub_1482 from that we can see that the key is always the same.
So let's check the encryptor function.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/3.png)

In this function we will find two functions the first one do some encoding to the key and the second one is more important as we can see the homework.txt data is being encoded here.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/4.png)

Looking to this function we can find that the encryption algrothim is just XOR operation with some constant values as we can see as the first byte of the file will always be Xored with the same value and as we know Xoring the the number with something twice will return the same number so we can decrypt our encrypted data without the key by just craeting a homework.txt file which contain the encrypted data. 
So the problem now that we are missing the encrypted data, the first i did  was searching about the core dump file as it was the first time work with one after some search i found that we can extract the arguments that is being passed to the decryptor functions using gdb.
So let's open the decryptor in ida and see where is encrypted data is being passed.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/5.png)

As we can see the encrypted data is one of the decrypt function arguments so we can extract it using the core file and after some digging i notced that the decrypt function is the same as the encryptor so our consumption was true.
So let's open the core file and extract the encrypted data.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/6.png)

After that we can use the backtrace or the info stack command to see all the functions calls after that we can select frame 2 which is the decrypt function then write info frame to see where is the arguments is being stored.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/7.png)

After some searching i found that the arguments is being stored as follow.
rbp+8 is the return pointer.
rbp+16 is the first argument which is in this function is the key.
rbp+24 is the second argument which is the encrypted data

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/8.png)

As we can see everything is fine but we couldn't extract the encrypted data so we can just go back to the main function frame and see where is encrypted data is being stored then extract the data.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/9.png)

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/10.png)

As we can see before the call of the decrypt function we see some mov isntructions we are intrested only to the mov to rcx register as it contain the encrypted data.
Now we can extract the encrypted data as it's being stored in rbp-0x20 addres.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/11.png)

Now we have the encrypted data all we have to do now is just creating a homework.txt file which contain these hex values.
All we have to do now is just execute the homeworkcrypter elf.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/12.png)

We know have the homework file decrypted success all we have to do know is just make a small script to split these hex values and print the ascii representation of each byte.
So i wrote a small script to make these steps.

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/13.png)

After runing our script we find our flag :)

![Image](https://raw.githubusercontent.com/joezid/joezid.github.io/master/Images/BsidesCTF2020/14.png)

# Flag

**HTB{b@d_b@d_Onii_chw@n_:(}**

