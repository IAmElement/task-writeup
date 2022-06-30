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

• It has x executable functions, including the x function.
• The code uses common Linux libraries: libc.so and ld-linux.so.


### Dependencies

* Describe any prerequisites, libraries, OS version, etc., needed before installing program.
* ex. Windows 10

### Installing

* How/where to download your program
* Any modifications needed to be made to files/folders

### Executing program

* How to run the program
* Step-by-step bullets
```
code blocks for commands
```

## Help

Any advise for common problems or issues.
```
command to run if program contains helper info
```

## Authors

Contributors names and contact info

ex. Dominique Pizzie  
ex. [@DomPizzie](https://twitter.com/dompizzie)

## Version History

* 0.2
    * Various bug fixes and optimizations
    * See [commit change]() or See [release history]()
* 0.1
    * Initial Release

## License

This project is licensed under the [NAME HERE] License - see the LICENSE.md file for details

## Acknowledgments
