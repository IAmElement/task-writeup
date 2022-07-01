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
- The functions that stand out are `main` `pwnable` `error` `set_canary` `socket` `write`
