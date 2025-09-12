---
layout: default
title: Bitshifted's runtime.exe
---

# Initial Assessment

Opened the file in DIE and it seems like the .data section is packed

[](1.png)

Dumped the data found here

[](2.png)

to be continued....


# YARA Rules written for the binary:



YARA`
rule BitShifted_ProcessInjection
{
  meta:
    author = "BitShifted"
    desc = "Detects loader that uses CreateProcessW/VirtualAllocEx/WriteProcessMemory/CreateRemoteThread; also looks for 'BitShifted' marker"
  strings:
    $marker = "BitShifted" wide ascii
  condition:
    pe.imports("KERNEL32.dll", "CreateProcessW") and
    pe.imports("KERNEL32.dll", "VirtualAllocEx") and
    pe.imports("KERNEL32.dll", "WriteProcessMemory") and
    pe.imports("KERNEL32.dll", "CreateRemoteThread") and
    $marker
}
`
