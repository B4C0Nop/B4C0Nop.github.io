---
layout: default
title: Memecat Battlestation Write-up
---

# Memecat Battlestation Write-up
First I noticed it was a .NET file so I went a head and opened it up in dnSpy. Looked around for a few seconds and easily spoted the first string
![Error Loading Image](practice/memecat/1.jpg)
This is the second stage that appears after entering the first string which was "RAINBOW"
![Error Loading Image](practice/memecat/2.jpg)
After looking in the same place for form 2 I noticed this string which appears to just be XOR'd with 'A'
![Error Loading Image](practice/memecat/3.jpg)
XORing the above string with 'A' gives us "Bagel_Cannon" enter that and we reach the victory screen with the flag
![Error Loading Image](practice/memecat/4.jpg)
The flag seems to have not loaded correctly but the challenge was solved either way.
