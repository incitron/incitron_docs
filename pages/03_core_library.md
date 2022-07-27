---
layout: default
title: 3. core library
nav_order: 3
has_children: false
---

# core incitron library
--------

## building & compiling
--------

incitron is compiled using the [CMake meta-build system](https://cmake.org/).
the core engine is developed and tested on Windows and Linux (it will most likely work on Mac, but hasn't been tested).
MSVC 2022 is used to compile on Windows, and GCC 10 on Linux (via CMake).

## testing
--------

the core incitron engine is tested using [catch2](https://github.com/catchorg/Catch2).
all the tests can be found in the /tests directory.

### SCIP testing
--------

The [SCIP library](https://www.scipopt.org/) is the core linear programming framework that incitron is built on top of (standing on the shoulders of giants...).
To ensure the standard SCIP solver (without any incitron extensions) is functioning correctly, a series of LP and MILP models are tested.

### pseudoflow algorithm testing
--------

The pseudoflow algorithm is utilised as the min-cut max-flow solver in the BZ algorithm.

### BZ algorithm testing
--------

The BZ algorithm is utilised by incitron to quickly solve the linear relaxations at nodes in the branch-and-bound tree.

## external libraries
--------

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

