`YARA
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
