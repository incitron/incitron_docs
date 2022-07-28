---
layout: default
title: 1. introduction
nav_order: 1
has_children: false
---

# incitron engine

`incitron` is a custom mixed-integer linear programming (MILP) solver for strategic mine scheduling problems.

General-purpose commercial linear programming solvers such as [CPLEX](https://www.ibm.com/analytics/cplex-optimizer) and [Gurobi](https://www.gurobi.com/) are commonly used for solving strategic mine planning problems. However, these solves are not optimised for solving mine scheduling problems, and solve times can become long with even non-trivial sized models. `incitron` overcomes the challengers faced by these other solvers because it is driven by a suite of custom algorithms that are fine-tuned for mine scheduling problems.

The core engine is written in C, with a command-line interface written in C++. The command-line interface is used to provide interfaces with other languages. The reason the engine was written in C instead of newer, higher-level languages such as [Python](https://www.python.org/), [C#](https://docs.microsoft.com/en-us/dotnet/csharp/), [Go](https://go.dev/), [Rust](https://www.rust-lang.org/) or [Julia](https://julialang.org/) was for the following reasons:
* C is close to the metal and extremely fast.
* Manual memory management allows for granular control over allocating and freeing memory.
* Some of the key libraries used by `incitron` (i.e., SCIP) are written in C, so it's easier to interface with these libraries.

--------
## features

Key features of the incitron engine include:
* Dynamic mining and processing cut-off grade optimisation
* Material blending 
* Capital expenditure decisions
* Stockpiling & reclaiming considered as part of the optimisation process
* Custom mining precedences between different mining areas
* Custom designed constraints for mine scheduling:
  * Grade blending constraints
  * Equipment utilisation leveling
  * Cashflow requirements
  * Capital expenditure limits

The fast solve times and accuracy of the incitron engine are driven by a suite of custom mining algorithms, such as:
* Bienstock-Zuckerberg (BZ) algorithm
* TopoSort heuristic algorithm

--------
## code

The full code repo is hosted on [Github](https://github.com/incitron/incitron).
Note for developers: you have to be granted developer access 
