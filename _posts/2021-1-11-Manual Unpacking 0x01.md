---
layout: post
title:  Manual Unpacking 0x01 (Self Injection)
categories: [Malware-Analysis]
tags: [Reverse Engineering,Malware Analysis]
description: Manual unpacking using tiny tracer and x64 dbg.
---
# Background
This time we will be talking about the first injection technique which is `Self Injection` ,From the name we can guess that the malware will inject the malicious payload to it's own process and then transfer the execution to the newly unpacked malicious code.

# Tools
We will mainly use three tools beside the tools we use regulary:

1- [x64 dbg](https://x64dbg.com/): No need for description as you all probley know.is 

2- [Scylla](https://github.com/NtQuery/Scylla): Will be used to dump the unpacked process and reconstruct the import table.

3- [Tiny Tracer](https://github.com/hasherezade/tiny_tracer): Will be used to trace API calls and trace transition between sections(helpful in finding OEP of the packed module).

# Injection Techniques
We have two common techniques which usually used here the first one is injecting the unpacked code to allocated memory and the second one is unpack the payload then overwrite one of the sections of the pe file.

***1- Section Overwrite:***


![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/packing_dia2.png)

The flow here is quite simple the malicious payload will be decompressed or decrypted then written to another section,This technique is used in one of the famous old packers which you all probaly know `UPX`.

Now let's see how we can unpack any sample that uses the section overwrite technique.

1- Use `Tiny Trace` to determine the `OEP` as it can be used to trace transition between sections.

2- Set a breakpoint on the `OEP` and let the program unpack it self.

3- Dump the unpacked section and fix the import table.

