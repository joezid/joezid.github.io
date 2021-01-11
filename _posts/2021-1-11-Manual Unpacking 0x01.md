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

1- [x64 dbg](https://x64dbg.com/): No need for description as you all probley know.

2- [Scylla](https://github.com/NtQuery/Scylla): Will be used to dump unpacked process and fix the import table.

3- [Tiny Tracer](https://github.com/hasherezade/tiny_tracer): Will be used to trace API calls and trace transition between sections(helpful in finding OEP of the packed module).

# Injection Techniques
We have two common techniques which usually used here the first one is injecting the unpacked code to allocated memory and the second one is unpack the payload then overwrite one of the section of the pe file.

