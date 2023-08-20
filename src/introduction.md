# Incitron engine

`incitron` is a custom mixed-integer linear programming (MILP) solver for strategic mine scheduling problems.

General-purpose commercial linear programming solvers such as [CPLEX](https://www.ibm.com/analytics/cplex-optimizer) and [Gurobi](https://www.gurobi.com/) are commonly used for solving strategic mine planning problems. However, these solves are not optimised for solving mine scheduling problems, and solve times can become long with even non-trivial sized models. `incitron` overcomes the challengers faced by these other solvers because it is driven by a suite of custom algorithms that are fine-tuned for mine scheduling problems.

The core engine is written in C, with a command-line interface written in C++. The command-line interface is used to provide interfaces with other languages. The reason the engine was written in C instead of newer, higher-level languages such as [Python](https://www.python.org/), [C#](https://docs.microsoft.com/en-us/dotnet/csharp/), [Go](https://go.dev/), [Rust](https://www.rust-lang.org/) or [Julia](https://julialang.org/) was for the following reasons:
* C is close to the metal and extremely fast.
* Manual memory management allows for granular control over allocating and freeing memory.
* Some of the key libraries used by `incitron` (i.e., SCIP) are written in C, so it's easier to interface with these libraries.

## Features

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
* Stockpile blending spatial branching algorithm
* Early/Late start cuts & preprocessing
* 1-Destination heuristic
* Re-optimise destination heuristic

## Why strategic mine planning?

A common question is: why do we need to bother with a strategic plan for our mine? This question is understandable, especially from the perspective of someone working at an operating mine where the main aim of the game is to ensure production targets are met, irrespective of all other outcomes. On top of this, while common sense suggests that the optimal mining sequence of a resource should be from high-grade to low-grade, and the economical cut-off and cut-over grades should be defined from Ken Lane's theorems, these basic rules of thumb do not always hold true and quickly fall apart in real world scenarios. What if there are multiple revenue-generating resource elements, or a set of deletirious elements that penalise the revenue (i.e. iron ore mining)? What if the revenue is directly proportional to the grade, but the processing yield is proportional to the rock type (i.e. gold mining)? What if running the plant has a fixed cost associated it, there are multiple capital decisions associated with expanding the plant and mining fleet, and expanding the plant will reduce the variable processing cost, all while there are bonus-penalties associated with processing different blocks of ore, how do you define the optimal mining and processing sequence...? As the questions about the optimal operating strategy of a mine become more complex, it becomes more obvious that mine planning engineers need advanced mathematical tools to help them answer these strategic questions with confidence and assurance that they have the right answer.

## Code

The full code repo is hosted on [Github](https://github.com/incitron/incitron).
Note for developers: you have to be granted developer access to be able to view the incitron code repo.
