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

Now let's unpack a sample packed by `UPX`.

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/up.PNG)

If you check the sections of any `UPX` packed sample you will notice that the section `UPX0` have a 0 raw size but at the same time have a large value when the executable is mapped into memory,So we can start guessing that the unpacked payload will be written to this section.

Now let's run `tiny tracer` and see where the section transition is done.

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/rn_p.png)

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/p_2.PNG)

```
Note: Leave the sample runinig for 30 seconds or 1 minute then terminate it.
```
After the termination of the process you will find a file generated with the extension `.tag` which contain all the api calls used by the traced sample,So let's check it.

```
215ef;kernel32.LoadLibraryA
21604;kernel32.GetProcAddress
21604;kernel32.GetProcAddress
21604;kernel32.GetProcAddress
21604;kernel32.GetProcAddress
21604;kernel32.GetProcAddress
21604;kernel32.GetProcAddress
21604;kernel32.GetProcAddress
21663;kernel32.VirtualProtect
21678;kernel32.VirtualProtect
216bd;[UPX1] -> [UPX0]
11136;section: [UPX0] ---> Our OEP
13792;kernel32.GetSystemTimeAsFileTime
137a7;kernel32.GetCurrentThreadId
137b3;kernel32.GetCurrentProcessId
137c3;kernel32.QueryPerformanceCounter
14dce;kernel32.IsProcessorFeaturePresent
148e0;CPUID:0
14956;CPUID:1
14a2a;CPUID:7
```
Checking the genrated tag file we can notice a transation happen from `UPX0` section to the section `UPX1` and our `OEP` will be at the RVA `0x11136` ,So let's load the executable in x64 dbg.


![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/bas.png)

As the executable is loaded at the base address `0x400000` so our `OEP` will be at the address `0x411136`.

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/add.png)

Now go to the address `0x411136` and set a hardware breakpoint on execution on this address and run the executable till the breakpoint is hit.

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/unp_1.PNG)

As we can notice our breakpoint is hitted and the code at this address is changed too,So let's use `Scylla` to dump the unpacked data and fix the import address table.

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/dump_1.png)


![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/dump_3.png)

Now you can start analyzing the unpacked sample as every thing is fine now.

```
So let's move to the second technique we will discuss :D
```

***2- Inject the unpacked payload to allocated memory:***

![Image](https://github.com/joezid/joezid.github.io/raw/main/Images/Manual%20unpacking/Self_1.gif)
