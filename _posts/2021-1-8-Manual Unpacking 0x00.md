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

***1- Signature Based Detection:***

This is the easiest way to check if the sample we are working with is packed using any common packer and this can be done using tools like `Exeinfo` , `Detect-It-Easy` or `Peid`. Usually, we will check the sample with more than one tool as each one of them may be using different signatures.

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/screenshot.jpg)

***2- Number Of Imports:***

Low number of imports can be an indicator of packing,But we can't be sure as maybe the malware author is using dynamic import resolving to resolve all the imports during the runtime.

`Unpacked Sample Imports`

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/unpac_imp.PNG)

`Packed Sample Imports`

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/pac_imp.PNG)

***3- Entropy Analysis:***

Shannon entropy can be a good indicator for detecting the use of packing, compression, and encryption in a file, The returned value will be between 0-8.
If the returned value is high this can be an indicator that the sample is packed.

`Unpacked Sample Entropy`

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/unpac_entr.PNG)

`Packed Sample Entropy`

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/pac_entr.PNG)

# Unpacking Steps

1- The first step is to identify the OEP,As we saw before at some point the decporession or the decryption will be finished and the execution will be continue from the OEP.

2- Execute the sample until we arrive to the OEP,Which will allow the sample to unpack it self.

3- Dump the unpacked process from memory.

4- Fix the import address table `IAT` of the dumped file.

