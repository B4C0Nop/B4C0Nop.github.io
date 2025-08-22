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


![Error Loading Image](baconTUT/01EntropyNotPacked.jpg)
image test
![Error Loading Image](baconTUT/02EntropyPacked.jpg)
![Error Loading Image](baconTUT/01EntropyNotPacked.jpg)
![Error Loading Image](baconTUT/03PEBearComparison.png)
![Error Loading Image](baconTUT/04GhidraBacontut.jpg)
![Error Loading Image](baconTUT/05GhidraBacontutUPX.jpg)
![Error Loading Image](baconTUT/06OpenX64dbg.jpg)
![Error Loading Image](baconTUT/07X64BacontutGraph.jpg)
![Error Loading Image](baconTUT/08X64BacontutBreakpoint.jpg)
![Error Loading Image](baconTUT/09X64BacontutRun.jpg)
![Error Loading Image](baconTUT/10X64BacontutCopyAddress.jpg)
![Error Loading Image](baconTUT/11X64BacontutScylla.jpg)
![Error Loading Image](baconTUT/12GhidraBacontutFLAG.jpg)
