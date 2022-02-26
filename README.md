# figulus
A small builder system based on C and Lua (WIP: currently in idea state)

## Background and idea for new tool

Make is nice, but defaults to building in source directory. Not only that, it requires a complete scan, rule deductions and more before it starts building the files. Moreover, it works directory for directory. And it is difficult to include optional dependencies like in linux-kernel. Ninja is great, but requires another software for the generation of it's build file. However, it starts working immediately and considers dependencies across directories. Cmake only generates build files for other tools, which are required in addition. Also, it is rather difficult to customize. However, it separates compiled files from source directories.

