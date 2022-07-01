# Reversal Task Writeup

This repoistory shows my progress through reversing an ELF executable IOT find an exploit.

## Initial Analysis

File delivered was initially analysed using Windows based tools to identify the following:
- What it does
- How it operates

First things first, I opened the file in Visual Studio Code in an attempt to identify at least the language it was written in. 
It seems like some level of compiled C# or C++ at this point, so we attempt to give it an extension so software like IDA Pro and dnSpy can decompile it. 
However, this errored and did not work as intended. Following the error, we know at this point this isn't a normal Windows executable. 

From here, I string dumped the file and identified various strings like `GNU` `Ubuntu` `GLIBC` `ELF`. I also identified various strings like `socket` `listen` `write`
which right away points to something malicious (given the file name is also `remote_pwn`)

Let's debunk the file on a Linux based system, as that seems like where it belongs.

## Main Reversal
First, we upload the file to a free file sharing service to easily get it over to the VM without using guest additions or similar. In a normal OPSEC respective environment we wouldn't do this,
however circumstances prevail. Initially, we want to learn the functions of the file to understand what it does. Through fetching .rodata, we were greeted with a lovely string `Oh no! Stack protection :(`

- It has 6 executable functions that are of interest, including the main function.
- The code uses common Linux libraries: libc.so and ld-linux.so.
- The functions that stand out are `main` `pwnable` `error` `socket` `write`

## Extended Reversal
There isn't a whole lot we can learn from the above aside from the key functions we need to learn more about. From here, let's further decompile the executable in Ghidra. From this, we've learnt there is some level of obfuscation (or appears to be) on the different functions. I've pulled the following code from the pwnable() function after decompiling it.

```c
void pwnable(char *param_1)

{
    int in_GS_OFFSET;
    char local_40c [1024];
    int local_c:

    local c = *(int *)(in  GS_OFFSET + 0x14);
    strcpy(local_40c, param_1);
    if (local_c != *(int *)(in  GS_OFFSET + 0x14)) {
        __stack_chk_fail_local();
    }
}```

It looks like this copies the string to a local buffer, checks if the stack is still in the same state - if it's not, it calls __stack_chk_fail_local(). If it is, it calls pwnable().

