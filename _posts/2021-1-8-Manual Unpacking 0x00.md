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

This is considred the basics steps you should know to be able to unpack the basics packers,But in most cases it will be hard to follow and understand the full unpacking routine so we move to the generic unpacking to save our time if we don't have to fully analyze the unpacking routine.

# Generic Unpacking

Malware authors always try to hide and evade detection not just by packing the code but also by using different injection techniques like `Self Injection` , `PE Injection` , `Process Hollowing` ,...etc.

Each technique of the injections we talked about requires a set of API calls to be achieved, So by monitoring the APIs calls made by the executable we can predict the technique used and the functionality of the sample, But for now we will talk about three APIs we can set a breakpoint on them and watch the parameters passed or the return value until we talk in details about each technique.

1- `VirtualAlloc` : This function is a memory-allocation routine that can allocate memory in a current process or in a remote process if using `VirtualAllocEx`, So for sure, it will be useful to see if the malware is allocating memory and view changes that happen to the allocated memory as maybe the unpacked pe file will be written to the allocated memory or a shellcode will be written there.

2- `VirtualProtect` : This function is used to change memory protection and usually follows the `VirtualAlloc`.

3- `CreateProcessInternalW` : A low-level API call that is used to create a new process, We can set a breakpoint there and see the parameters passed to the API.





