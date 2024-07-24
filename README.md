# figulus
A small builder system based on C and Javascript (WIP: currently in idea state)

## Background and idea for new tool

Make is nice, but defaults to building in source directory. Not only that, it requires a complete scan, rule deductions and more before it starts building the files. Moreover, it works directory for directory. And it is difficult to include optional dependencies like in linux-kernel. Ninja is great, but requires another software for the generation of its build file. However, it starts working immediately and considers dependencies across directories. Cmake only generates build files for other tools, which are required in addition. Also, it is rather difficult to customize. However, it separates compiled files from source directories.

## Requirements

* fast startup using a cache-file in the root directory
* generating cache files using javascript and additional functions
    * javascript files in directories describe build processes
    * general functions defined in system folders
    * simple datastructure that will be compiled to a cache-file at the end
* starting in subdirectories
* supporting several targets
* easy concept
* optional:
    * watching changes and starting rebuilds (inotify)
    * nice output (ncurses)

## Why Javascript?

Programmers write code and data structures and imperative programming is natural to them. 
Special coding for build systems is a hassle. 
[MuJS](https://mujs.com/) is small. 
It has chunks, which allows the split of code into several files. 
It is fast. 
*MuJS* is open source and licensed under a custom license, which is quite permissible. 
*MuJS* like *C* does not comprise more than a few simple functions, but easily can be extended. 

## Build Data structure

*Figulus* executes the *JavaScript* files. 
Those can invoke other files, read directories and simple option files.
Functions can be defined and executed.

At the end, a global variable "targets" contains the necessary commands, dependencies and options to build the targets.

~~~JSON
{
  "default": {
    "subtargets": [ "hello" ]
  },
  "hello": {
    "inputs": [ "hello.c", "hello.h" ],
    "outputs": "hello",
    "program": [ "gcc", "-o", "hello", "hello.c" ]
    "wd" : "/tmp/prog/fig/"
  }
}
~~~

At the end, this will be translated into
~~~Txt
t default 
s hello
t hello
< hello.c
< hello.h
> hello
! gcc
! -o
! hello
! hello.c
d /tmp/prog/fig
~~~

## Functional design

At program start, the cache file is read in and necessary programs run when dependent files are outdated.
The cache file structure is simple, each line contains one character and the remainder of the line contains the string argument: file name, or the array elements for *execvpe*.

In a different thread, *MuJS* executes the javascript chunks and compile a new cache structure. 
It writes that structure to file and also replaces the internal structure.

Moreover, it continues to watch the directory so it can update the dependencies automagically. 

