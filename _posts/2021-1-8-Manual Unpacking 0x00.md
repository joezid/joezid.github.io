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

From the image above we can understand the mechanism of how regular packers work,First it compress the actual exectuable and inject the compressed data to the new pe file then set the entry point to the decompression or decryption stub which will be used the to decompress the code and jump to the original entry point `OEP`.

# Packing Detection

```
Signature Based Detection
```
