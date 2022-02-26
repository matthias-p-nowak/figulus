# figulus
A small builder system based on C and Lua (WIP: currently in idea state)

## Background and idea for new tool

Make is nice, but defaults to building in source directory. Not only that, it requires a complete scan, rule deductions and more before it starts building the files. Moreover, it works directory for directory. And it is difficult to include optional dependencies like in linux-kernel. Ninja is great, but requires another software for the generation of it's build file. However, it starts working immediately and considers dependencies across directories. Cmake only generates build files for other tools, which are required in addition. Also, it is rather difficult to customize. However, it separates compiled files from source directories.

## Requirements

* fast startup using a cache
* generating cache files using lua
    * lua files in directories describe build processes
    * general functions defined in system folders
    * simple datastructure that will generate the cache-file at the end
* starting in subdirectories
* supporting several targets
* easy concept
* optional:
    * watching changes and starting rebuilds (inotify)
    * nice output (ncurses)

## Why Lua?

Programmers write code and data structures and imperative programming is natural to them. Special coding for build systems is a hassle. Lua is small. It has chunks, which allows the split of code into several files. It has just enough for writing the cache file. It is fast. Lua is open source and licensed under MIT-license. Not only that, compiling Lua from source code

## Build Data structure

Lua's basic datastructure is a table.

~~~Lua
targets={ 
  default={ 
    subtargets ={ 'this'}
    },
  this= {
    subtargets={},  
    inputs = { 'hello.c','hello.h'},
    outputs = { 'hello'},
    commands = { 'gcc', '-o', 'hello', 'hello.c' }
    }
  }
~~~

At the end, this will be translated into
~~~Txt
# comment
t default this
t this
< hello.c
< hello.h
> hello
! gcc
! -o
! hello
! hello.c
~~~

