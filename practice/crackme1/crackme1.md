---
layout: default
title: ByteClassic Crackme Write-up
---

# ByteClassic Crackme Write-up
Got this crack me from https://crackmes.one/crackme/68503ad02b84be7ea7743936 and it seems to have an accompanying YouTube video here: https://www.youtube.com/watch?v=AN3Fhum05fA

First step is to put it in DiE and check the entropy to see if it is packed
![Error Loading Image](/practice/crackme1/1.png)
The file does not appear to be pack but consists of quite a few sections. Next step we open it in Ghidra
![Error Loading Image](/practice/crackme1/2.png)
Well.. that was easy. If we just look to the right at the dissasembly of main we see the password is simply "secret123",
This file took 100x longer to analyze than it took to solve. Maybe I should have used HxD instead of Ghidra like was recommnended in the video but I only found that after I had solved it. Oh well
