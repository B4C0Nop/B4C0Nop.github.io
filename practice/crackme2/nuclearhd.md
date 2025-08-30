---
layout: default
title: nuclearhd Crackme Write-up
---

# nuclearhd Crackme Write-up

Link:  https://crackmes.one/crackme/6834cbfb6297cca3ff7d7cd5

First step is to put it in DiE and check the entropy to see if it is packed
![Error Loading Image](/practice/crackme2/1.png)
The File is clearly not packed so lets open it up in Ghidra.
# Main Function Overview:
![Error Loading Image](/practice/crackme2/2.png)
The program generates a numeric password from the username, but it never actually displays the password. Instead, it calculates it internally using the username. Because the password isn’t shown, we need to reverse-engineer the generation logic to predict what the password will be for any given username ahead of time.

Notice the generate_password function in main. lets RE that next
![Error Loading Image](/practice/crackme2/3.png)

- Interesting. So that's all the information we need to solve the Crackme. Allow me to explain.
# Function Signature

```c
void generate_password(string *param_1, int *param_2)

```
- `param_1`: pointer to `std::string` (the input string).

- `param_2`: pointer to an `int` (where the result will be stored).

# Local Variables
```c
int local_10;   // will hold string length
int local_c;    // running sum of character codes
char local_19;  // temporary char storage
```
Other variables (`local_28`, `local_30`, etc.) are just iterators used to walk through the string.

## Step 1: Get the string

```c
uVar1 = std::__cxx11::string::length(param_1);
local_10 = (int)uVar1;

```
So `local_10` = length of the string

## Step 2: Iterate over characters

```c
local_28 = std::__cxx11::string::begin(param_1);
local_30 = std::__cxx11::string::end(param_1);

while ( local_28 != local_30 ) {
    pcVar2 = (char *)__gnu_cxx::__normal_iterator<>::operator*((...)&local_28);
    local_19 = *pcVar2;
    local_c = local_c + local_19;
    __gnu_cxx::__normal_iterator<>::operator++((...)&local_28);
}

```

This is just a for-loop over each character in the string.

For each character:

- Take the ASCII code (`local_19 = *pcVar2`).

- Add it to the running sum (`local_c`).

So `local_c` becomes the sum of all ASCII values of the string’s characters.


## Step 3: Compute result

```cpp
*param_2 = local_c * local_10;
```

Finally, it multiplies:

- Sum of ASCII codes of characters

- × Length of the string

And writes the result into `*param_2.`

## TL;DR
`generate_password` takes a string, computes the sum of all its character codes, multiplies that sum by the string’s length, and stores the result in the provided integer pointer.

# Example

```cpp
string s = "ABC";   // 'A'=65, 'B'=66, 'C'=67
sum = 65 + 66 + 67 = 198
length = 3
result = 198 * 3 = 594
```

So `param_2` = 594.

## Solution
If we input the username 'bacon' our password would be '2575'

# Why?

Let’s work it out with "bacon":

Characters -> ASCII values:

'b' = 98

'a' = 97

'c' = 99

'o' = 111

'n' = 110

Sum = 98 + 97 + 99 + 111 + 110 = 515

Length = 5

Result = 515 × 5 = 2575
