---
layout: default
title: Command line interface
nav_order: 4
has_children: false
---

# Command line interface (CLI)

incitron is usually driven through a command line interface and a JSON configuration file.

## CLI options

The table below lists out all the command line options that can be passed to the command line interface.
An example of passing a JSON configuration file to the CLI is shown below.

```bash
./incitron --configfile scenario1.json
```

|  CLI long options   |  CLI short options  | Description                                                                                          |
|---------------------|---------------------|------------------------------------------------------------------------------------------------------|
| \-\-help            | \-h                 | prints to the console information about incitron engine and the list of command line options         |
| \-\-version         | \-v                 | print the incitron engine version to the console                                                     |
| \-\-configfile      | \-c                 | use a json file as the input configuration to run the incitron algorithm                             |

## Config file specification

This section describes the JSON schema used by the command-line interface version of incitron for configuring an optimisation run. Note that both the input data and configuration are contained within the same JSON file.

