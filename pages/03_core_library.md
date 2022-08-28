---
layout: default
title: 3. core library
nav_order: 3
has_children: false
---

# core incitron library

## building & compiling

incitron is compiled using the [CMake meta-build system](https://cmake.org/).
The core engine is developed and tested on Windows 10, Mac and Linux (Ubuntu).
The compilers used are:
* Windows: MSVC 2022
* Mac: LLVM 14 (Clang)
* Linux: GCC 10

The build system used across all platforms is [Ninja](https://ninja-build.org/).
You should download a binary of Ninja and make sure it's in your system PATH for CMake to be able to find.

### windows (msvc)

To setup MSVC 2022, download the Visual Studio Build Tools from [Microsoft](https://visualstudio.microsoft.com/downloads/).
Within the Build Tools, download and install the C++ development tools.
A good guide by Microsoft can also be found at this link: [MSVC install guide](https://docs.microsoft.com/en-us/cpp/build/building-on-the-command-line).

### mac (clang)

Using a terminal, install the Xcode command line tools:
```bash
$ sudo xcode-select --install
```

Using [brew](), install the current version of LLVM and ensure its in your system's PATH:
```bash
$ brew install llvm
```
```bash
$ echo 'export PATH="/opt/homebrew/opt/llvm/bin:$PATH"' >> ~/.zshrc
```

### linux (gcc)

The GNU Compiler Collection (GCC) is a collection of compilers and libraries for C and C++ development.

Install the build-essentials for Linux:
```bash
$ sudo apt install build-essential
```

Install the specific version of GCC:
```bash
$ sudo apt install gcc-10
```

## testing

The core incitron engine is tested using [catch2](https://github.com/catchorg/Catch2). 
All the tests can be found in the `/tests` directory.

## external libraries

This section describes the dependencies for incitron, and where they can be downloaded. 
For original archived copies of the external libraries please see the directory lib/archives.

|    Library                                                                |                Description/Use                | Language |   Version   |   Date Downloaded  |
|---------------------------------------------------------------------------|-----------------------------------------------|----------|-------------|--------------------|
| [catch2](https://github.com/catchorg/Catch2)                              | unit testing framework                        |    C++   | 2.13.4      | 08/01/2020         |
| [scip](https://www.scipopt.org/index.php#download)                        | linear programming framework                  |    C     | 7.0.2       | 29/01/2021         |
| [soplex](https://soplex.zib.de/index.php#download)                        | linear program solver                         |    C++   | 5.0.2       | 29/01/2021         |
| [cxxopts](https://github.com/jarro2783/cxxopts)                           | command-line options parser                   |    C++   | 3.0.0       | 28/03/2021         |
| [json](https://github.com/nlohmann/json)                                  | json parser for command line interface        |    C++   | 3.9.1       | 28/03/2021         |
| [sds](https://github.com/antirez/sds)                                     | simple dynamic strings - C string library     |    C     | 2.0.0       | 01/05/2021         |
| [bliss](https://github.com/ds4dm/Bliss)                                   | symmetry handling for SCIP search tree        |    C     | 0.73.3      | 05/02/2021         |

