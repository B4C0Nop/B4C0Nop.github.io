I recently created this blog in preparation for Flare-on 2025.  
To practice writing technical write-ups, I decided to create a tutorial on how to manually unpack UPX. In this tutorial, we’ll take a simple C program, pack it with UPX, and then manually unpack it using x64dbg, step by step.

So what is [UPX](https://github.com/upx/upx)?  
**UPX** stands for The **U**ltimate **P**acker for e**X**ecutables. It's a free and open-source executable packer that can typically reduce the size of programs and DLLs by **50%-70%**.

But what is a packer?  
Packers, also called runtime packers or self-extracting archives, are software that unpack themselves in memory when executed. This technique is sometimes referred to as executable compression, and it was originally invented to make files smaller so users wouldn’t have to unpack them manually.  
For more info, check out [Malwarebytes](https://www.malwarebytes.com/blog/news/malware/2017/03/explained-packer-crypter-and-protector).

So how do we manually unpack a binary?
In the simplest of terms, we will basically run the packed binary to let the unpacking stub unpack the binary for us and then dump the process to disk and manually fix the PE header. Usually this is done in three simple steps:
1. Find the OEP
2. Fix the IAT
3. Dump the binary from memory to disk

There are various methods of doing this but I will be using a fairly simple one that involves x64dbg and scylla.

##Setup##

#Tools#
x64dbg
ghidra
DIE
PEBear
#Creating a simple program
We'll start with a minimal C program that prompts the user for a password and compares it to a hardcoded string `flag{bacon}` This will be our test program for packing and unpacking.

Source Code:

```c
#include <stdio.h>
#include <string.h>

int main() 
{
    char input[32];
    const char *flag = "flag{bacon}";

    printf("Enter the password: ");
    gets(input);  
	
    if (strcmp(input, flag) == 0) {
        printf("Correct!\n");
    } else {
        printf("Wrong!\n");
    }

    return 0;
}
```
#Packing with UPX#
Next, after compiling the source above, we will pack the resulting binary with UPX.


#Step 0 How to tell if a binary is packed with UPX
To determine if your binary is packed or not there are some indicators to look out for which will indicate that the binary is packed. The first of which is checking the binary's entropy.

Entropy is a measure of randomness and the higher the entropy the more random the data is, usually indicating that it is encoded or encrypted. The rule of thumb is that, if the entropy is 6.5 and above this is an indicator that the sample may be packed. 

For this step we will open the binary with DIE(Detect It Easy) and check the entropy

This first image is of the binary before we packed it with UPX. Notice the entropy is 6.13 which is below the 6.5 rule of thumb
##Unpacked##
![Error Loading Image](baconTUT/01EntropyNotPacked.jpg)
This image below is of the binary after it was packed. Notice the entropy is 7.86 which is above the 6.5 rule of thumb

##Packed##
![Error Loading Image](baconTUT/02EntropyPacked.jpg)

For this next step we open the binary in PEBear
Another easy to spot indicator is the sections. Notice the comparison of the unpacked(bacontut) and packed(bacontutUPX) binary below. the unpacked binary has normal section headers(.text, .rdata, .data, etc.) and the packed binary has the sections(UPX0, UPX1, UPX2)

##PEBear Comparison##
![Error Loading Image](baconTUT/03PEBearComparison.png)

Other indicators to look out for in PEBear, which were not included in the screenshots, are high compression ratios, which often indicates that a packer has been used because the binary has been significantly reduced in size, and the differences between Raw Size and Virtual Size in the section headers.
**Raw Size** is how much space the section takes in the file on disk.
**Virtual Size** is how much memory it will occupy when loaded.
For packed binaries, the Raw Size of a compressed section like .UPX0 is much smaller than its Virtual Size because it will expand in memory once decompressed. In contrast, normal binaries have similar Raw and Virtual sizes as shown in the screenshot above.

Now for the other Idicators we will open the binary in Ghidra. Below is a screenshot of a unpacked binary(bacontut.exe) opened in Ghidra. Pay careful attention to the Imports on the left, the listing view in the middle and the Strings on the right.

![Error Loading Image](baconTUT/04GhidraBacontut.jpg)

Now compare the above image to the one below. Notice how there are very little Imports and Functions. Now if you look at the listing view you will see that it  looks like a bunch of junk and makes no sense. It was successfully packed and obfuscated though as we can tell by the upx1 text segment. Next look at the right and see how very little strings there are compared to the unpacked binary. 
![Error Loading Image](baconTUT/05GhidraBacontutUPX.jpg)

After considering those indicators we can now determine whether or not the binary is packed and move on to step 1

##Step 1 Find the OEP
Now we will open the packed binary in X64debug. Below is a simple screenshot of what it looks like when you open the binary in x64debug
![Error Loading Image](baconTUT/06OpenX64dbg.jpg)

Next we will need to look for the OEP. A simple way of doing this is pressing `g` to go into the graph view. What we are looking for is a jmp instruction before a giant block of code like the one below. If you do not see the giant code block just keep stepping and going back into graph view until you do. You will know it when you see it.
![Error Loading Image](baconTUT/07X64BacontutGraph.jpg)

After finding the giant block of code we want to select the jump intruction before it and set a breakpoint.
![Error Loading Image](baconTUT/08X64BacontutBreakpoint.jpg)

Now we want to hit run and reach our breakpoint
![Error Loading Image](baconTUT/09X64BacontutRun.jpg)

After reaching our breakpoint next press step into and then copy the address of RIP
![Error Loading Image](baconTUT/10X64BacontutCopyAddress.jpg)

![Error Loading Image](baconTUT/11X64BacontutScylla.jpg)
![Error Loading Image](baconTUT/12GhidraBacontutFLAG.jpg)
