---
layout: post
title:  Manual Unpacking 0x00 (Intro)
categories: [Malware-Analysis]
tags: [Reverse Engineering,Malware Analysis]
description: Intro To Manual Unpacking.
---
# Background
A Packer is a program that takes the executable as input, and it uses compression to obfuscate the executable's content and the actual content of the executable will be decompressed and executed during run time.

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/ezgif.com-gif-maker.png)

From the image above we can understand the mechanism of how regular packers work, First it compresses the actual executable and inject the compressed data to the new pe file then set the entry point to the decompression or decryption stub which will be used to decompress the code and jump to the original entry point `OEP`.

# Packing Detection

**1- Signature Based Detection:**

This is the easiest way to check if the sample we are working with is packed using any common packer and this can be done using tools like `Exeinfo` , `Detect-It-Easy` or `Peid`. Usually, we will check the sample with more than one tool as each one of them may be using different signatures.

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/screenshot.jpg)

**2- Number Of Imports:**

Low number of imports can be an indicator of packing,But we can't be sure as maybe the malware author is using dynamic import resolving to resolve all the imports during the runtime.

`Unpacked Sample Imports`

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/unpac_imp.PNG)

`Packed Sample Imports`

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/pac_imp.PNG)


