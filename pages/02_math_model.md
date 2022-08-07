---
layout: default
title: 2. math model
nav_order: 2
has_children: false
---

{% include mathjax.html %}

# mathematical model

This page describes the mathematical representation of the `MILP` model that is solved by the incitron engine. This is a `Precedence Constrained Production Scheduling Problem (PCPSP)` with stockpiling. Please take note of the special use of completing mining blocks and parcels `by` a time period, rather than `at` a time period.

**Table of contents**
* [indices and sets](#indices-and-sets)
* [decision variables](#decision-variables-by-format)
* [additional variables](#additional-variables-by-format)
* [objective function](#objective-function-by-format)
* [constraints](#constraints-by-format):
  * [Mine Each Block Only Once](#1-mine-each-block-only-once-finitude-constraint)
  * [Mine Each Parcel Only Once](#2-mine-each-parcel-only-once-finitude-constraint)
  * [Precedence Constraint](#3-precedence-constraint)
  * [Linking Constraint](#4-linking-constraint)
  * [Equal-Parcel Constraint](#5-equal-parcel-constraint)
  * [Stockpile Flow-Balancing Constraint](#6-stockpile-flow-balancing-constraint)
  * [Stockpile Mixing Constraint](#7-stockpile-mixing-constraint-equal-out-fractions)
  * [General Side Constraints](#8-general-side-constraints)
* [additional notes](#additional-notes):
  * [definition of blocks, parcels and destinations](#definition-of-blocks-parcels-destinations)
  * [Why does the linear program have integer terms?](#integer-terms-in-the-linear-program)
  * [AT-format and BY-format](#at-format-and-by-format)
  * [stockpiling](#stockpiling)

## indices and sets

$$ b \in B : $$ blocks; $$~1,...,B $$

$$ \hat{b} \in \hat{B}_b : $$  blocks that must be mined directly before block b; $$~1,...,\hat{B}_b $$

$$ p \in P_b : $$ parcels contained within block b; $$~1,...,P_b $$

$$ t \in T : $$ time periods; $$~1,...,T $$

$$ d \in D : $$ destinations; $$~1,...,D $$

$$ s \in S : $$ stockpiles; $$~1,...,S $$

$$ q \in Q : $$ side constraints; $$~1,...,Q $$

$$ c \in C : $$ capital expenditure options; $$~1,...,C $$

$$ \hat{q}_c \in \hat{Q}_c : $$ side constraints expanded by purchasing capital option c; $$~1,...,\hat{Q}_c ; \hat{Q}_c \subseteq Q $$

$$ f \in F : $$ fixed costs; $$~1,...,F $$

$$ \hat{p}_f \in \hat{P}_{bf} : $$ parcels that can trigger fixed cost f; $$~1,...,\hat{P}_{bf} ; \hat{P}_{bf} \subseteq P_b $$

$$ \hat{d}_f \in \hat{D}_f : $$ destinations that can trigger fixed cost f; $$~1,...,\hat{D}_f ; \hat{D}_f \subseteq D $$

## decision variables (by-format)

$$x_{\mathrm{bt}} : $$ 1 if block `b` has been mined by the end of period `t`, otherwise 0.

$$y_{\mathrm{bptd}} : $$ fraction of parcel `p` contained in block `b` mined by the end of period `t` and sent to destination `d`. 

$$z_{\mathrm{bpts}} : $$ fraction of parcel `p` contained in block `b` mined by the end of period `t` and sent to stockpile `s`. 

$$z_{\mathrm{sptd}} : $$ fraction of parcel `p` contained in stockpile `s` reclaimed by the end of period `t` and sent to destination `d`. 

$$f_{\mathrm{st}} : $$ relative proportion of parcels from the stockpile `s` reclaimed in time period `t` (i.e., "out-fraction").

$$u_{\mathrm{c\hat{q}}} : $$ 1 if capital decision `c` has been purchased to expand the capacity of a set of constraints $$\hat{q}$$, otherwise 0.

$$v_{\mathrm{f\hat{p}_f\hat{d}_f}} : $$ 1 if fixed cost has been paid to allow subset of parcels $$\hat{p}_f$$ contained in blocks `b` mined by period `t` to be sent to subset of destinations $$\hat{d}_f$$, otherwise 0.

precedence variables (binary) : $$ x_{\mathrm{bt}}\in\{0,1\} $$

decision variables (continuous) : $$ y_{\mathrm{bptd}}\in[ \,0,1] $$

stockpiling variables (continuous) : $$ z_{\mathrm{bpts}}\in[ \,0,1] $$

reclaiming variables (continuous) : $$ z_{\mathrm{sptd}}\in[ \,0,1] $$

reclaiming proportions (continous) : $$f_{\mathrm{st}}\in[ \,0,1] $$

capital decision variables (binary) : $$ u_{\mathrm{c\hat{q}}}\in\{0,1\} $$

fixed cost variables (binary) : $$ v_{\mathrm{f\hat{p}_f\hat{d}_f}}\in\{0,1\} $$

## additional variables (by-format)

$$p_{\mathrm{bptd}}^y : $$ objective value of parcel `p` contained in block `b` mined by period `t` and sent to destination `d`. 

$$p_{\mathrm{bpts}}^z : $$ objective value of parcel `p` contained in block `b` mined by period `t` and sent to stockpile `s`. 

$$p_{\mathrm{sptd}}^z : $$ objective value of parcel `p` contained in stockpile `s` reclaimed by period `t` and sent to destination `d`. 

$$p_{\mathrm{c\hat{q}}}^u : $$ objective value of capital decision `c`. 

$$p_{\mathrm{f\hat{p}_f\hat{d}_f}}^v : $$ objective value of fixed cost `f`. 

note that $$p$$ is usually the discounted value of making this decision, however it can be any value (recovered product, etc...)

## objective function (by-format)

$$ max~\displaystyle\sum_{b\in B} \displaystyle\sum_{p\in P_b} \displaystyle\sum_{t\in T} \displaystyle\sum_{d\in D} p_{\mathrm{bptd}}^y y_{\mathrm{bptd}} + \\~~~~~~~~ \displaystyle\sum_{b\in B} \displaystyle\sum_{p\in P_b} \displaystyle\sum_{t\in T} \displaystyle\sum_{s\in S} p_{\mathrm{bpts}}^z z_{\mathrm{bpts}} + \\~~~~~~~~ \displaystyle\sum_{s\in S} \displaystyle\sum_{p\in P_b} \displaystyle\sum_{t\in T} \displaystyle\sum_{d\in D} p_{\mathrm{sptd}}^z z_{\mathrm{sptd}} + \\~~~~~~~~ \displaystyle\sum_{c\in C} p_{\mathrm{c\hat{q}}}^u + \\~~~~~~~~ \displaystyle\sum_{f\in F} p_{\mathrm{f\hat{p}_f\hat{d}_f}}^v $$

## constraints (by-format)

### 1) Mine Each Block Only Once (finitude constraint)

$$For~b \in B,~t \in \{ 2,...,T \}: $$

$$ x_{\mathrm{bt-1}} \leq x_{\mathrm{bt}}~which~is~equivalent~to:~x_{\mathrm{bt-1}} - x_{\mathrm{bt}} \leq 0 $$

### 2) Mine Each Parcel Only Once (finitude constraint)

Note here we treat sending parcels to stockpiles as a seperate set of constraints (with the last stockpile S being equivalent to the last destination index), but realistically each stockpile could just be considered as another "destination".

$$For~b \in B,~p \in P_b,~t \in T,~d \in \{ 1,...,D-1 \}: $$

$$ y_{\mathrm{bptd}} \leq y_{\mathrm{bptd+1}}~which~is~equivalent~to:~y_{\mathrm{bptd}} - y_{\mathrm{bptd+1}} \leq 0 $$

$$For~b \in B,~p \in P_b,~t \in T,~d = D,~s = 1: $$

$$ y_{\mathrm{bptD}} \leq z_{\mathrm{bpts}}~which~is~equivalent~to:~y_{\mathrm{bptD}} - z_{\mathrm{bpts}} \leq 0 $$

$$For~b \in B,~p \in P_b,~t \in T,~s \in \{ 2,...,S-1 \}: $$

$$ z_{\mathrm{bpts}} \leq z_{\mathrm{bpts+1}}~which~is~equivalent~to:~z_{\mathrm{bpts}} - z_{\mathrm{bpts+1}} \leq 0 $$

$$For~b \in B,~p \in P_b,~t \in \{ 1,...,T-1 \},~s = S: $$

$$ z_{\mathrm{bptS}} \leq z_{\mathrm{bp+1tS}}~which~is~equivalent~to:~z_{\mathrm{bptS}} - z_{\mathrm{bp+1tS}} \leq 0 $$

### 3) Precedence Constraint

This constraint ensures that all the preceeding blocks have been mined before the parcels in the current block can be started.

$$For~b \in B,~p \in P_b,~\hat{b} \in \hat{B}_b,~t \in T,~s = S: $$

$$ z_{\mathrm{bptS}} \leq x_{\mathrm{\hat{b}t}}~which~is~equivalent~to:~z_{\mathrm{bptS}} - x_{\mathrm{\hat{b}t}} \leq 0 $$

### 4) Linking Constraint

This constraint links the block with the parcels contained within the block (i.e., once all the parcels are completely mined within a block, said block is allowed to take a value of 1).

$$For~b \in B,~p \in P_b,~t \in T,~s = S: $$

$$ x_{\mathrm{bt}} \leq z_{\mathrm{bptS}}~which~is~equivalent~to:~x_{\mathrm{bt}} - z_{\mathrm{bptS}} \leq 0 $$

### 5) Equal-Parcel Constraint

This constraint ensures that parcels in a block are mined in equal proportions (i.e., don't just mine all the ore parcels first and leave all the waste).

$$For~b \in B,~p \in \{ 2,...,P_b \},~t \in T,~s = S: $$

$$ z_{\mathrm{bp-1tS}} \leq z_{\mathrm{bptS}}~which~is~equivalent~to:~z_{\mathrm{bp-1tS}} - z_{\mathrm{bptS}} \leq 0 $$

### 6) Stockpile Flow-Balancing Constraint

This constraints ensures that the amount of a parcel that leaves a stockpile must be the same or less than the amount of said parcel that has entered the stockpile in previous periods.

$$For~b \in B,~p \in P_b,~t \in T,~s \in S,~d = D: $$

$$ z_{\mathrm{sptD}} \leq z_{\mathrm{bpts}}~which~is~equivalent~to:~z_{\mathrm{sptD}} - z_{\mathrm{bpts}} \leq 0 $$

### 7) Stockpile Mixing Constraint (equal out-fractions)

This constraint ensures that stockpiles are mixed (i.e., the proportion of parcels reclaimed from each stockpile in each period must be the same). Note that in the first period we have to ensure any pre-existing stockpile balances are reclaimed equally. Note that stockpile mixing is a non-linear constraint, but can be solved with a MILP branch-and-bound algorithm framework, as described by [Bley et. al., 2012]({{ site.url }}/assets/papers/Bley_etal_2012.pdf){:target="_blank"}.

$$For~b \in B,~p \in P_b,~t = 1,~s \in S,~d = D: $$

$$ z_{\mathrm{sp1D}} = f_{\mathrm{s1}} $$

$$For~b \in B,~p \in P_b,~t \in \{ 2,...,T \},~s \in S,~d = D: $$

$$ \frac{z_{\mathrm{sptD}} - z_{\mathrm{spt-1D}}}{z_{\mathrm{bpt-1s}} - z_{\mathrm{spt-1D}}} = f_{\mathrm{st}} $$

**Solving non-linear stockpiling mixing constraints:**

A special branching scheme is used to force the maximum violation of the nonlinear constraints arbitrarily close to zero, as described by [Bley et. al., 2012]({{ site.url }}/assets/papers/Bley_etal_2012.pdf){:target="_blank"}. The special branching method only adds linear constraints at each node in the branch-and-bound tree, so is practically efficient to solve using modern MILP solvers.

Assuming we have a valid linear relaxation solution (fractional) that violates the stockpile mixing constraint for a certain time period. Then there exists two parcels $$ i,j \in P $$ with different out-fractions (i.e., different values for $$ f_{\mathrm{st}} $$). Thus there must be a ratio:

$$ \frac{z_{\mathrm{sitD}} - z_{\mathrm{sit-1D}}}{z_{\mathrm{bit-1s}} - z_{\mathrm{sit-1D}}} \lt \phi \lt \frac{z_{\mathrm{sjtD}} - z_{\mathrm{sjt-1D}}}{z_{\mathrm{bjt-1s}} - z_{\mathrm{sjt-1D}}} $$

This inequality creates two branches at that node in the B&B tree:

Force the out-fractions of all parcels from a stockpile in a period to be <u>no more</u> than $$ \phi $$:

$$ z_{\mathrm{sptD}} + (\phi - 1) z_{\mathrm{spt-1D}} \leq  \phi z_{\mathrm{bpt-1s}}~which~is~equivalent~to:~ z_{\mathrm{sptD}} + (\phi - 1) z_{\mathrm{spt-1D}} - \phi z_{\mathrm{bpt-1s}} \leq 0 $$

Force the out-fractions of all parcels from a stockpile in a period to be <u>no less</u> than $$ \phi $$:

$$ z_{\mathrm{sptD}} + (\phi - 1) z_{\mathrm{spt-1D}} \geq  \phi z_{\mathrm{bpt-1s}}~which~is~equivalent~to:~ z_{\mathrm{sptD}} + (\phi - 1) z_{\mathrm{spt-1D}} - \phi z_{\mathrm{bpt-1s}} \geq 0 $$

For simplicity we usually choose a value of $$ \phi $$ that is the mean of the minimum and maximum out-fractions from a stockpile in a period.

### 8) General Side Constraints

* attribute sum
* attribute average
* mine block after period
* don't mine block after period
* mine block before period
* don't mine block before period

# additional notes

This section contains some additional notes regarding the mathematical model behind `incitron`.
Specific topics include:
* [definition of blocks, parcels and destinations](#definition-of-blocks-parcels-destinations)
* [Why does the linear program have integer terms?](#integer-terms-in-the-linear-program)
* [AT-format and BY-format](#at-format-and-by-format)
* [stockpiling](#stockpiling)

## definition of blocks, parcels, destinations

Mine planning is a classic example of the Precedence Constrained Production Scheduling Problem (PCPSP), where there are a set of activities (or "jobs") that must be scheduled to occur over a number of periods, with precedences between the activities. Further, each activity can have a number of different methods (or "ways", "modes") that it can be completed, and we must also choose the optimal method for completing said activity.

Within mine planning, the most common activity that must be scheduled is the mining of a "block" of earth. Note here that a block is not restricted to meaning an SMU block within a blockmodel, or even a block in an open pit mine. A block can represent any physical volume of rock at a mine, including:
* an SMU block on a bench
* a mining polygon on a bench in an open pit mine
* a whole bench in an open pit mine
* a whole phase at an open pit mine
* a stope at an underground mine
* a section of development at an underground mine
* a whole underground panel of mining
* a panel of a block cave mine
* etc...

Once a block has been mined, there is a secondary decision that must be made as to what should be done with the material, typically referred to as the "destination" of the block. Again, note here that a "destination" is not just restricted to a physical destination at a mine (i.e., the waste dump, the plant, etc), but should encompass any downstream decisions that are required to be made once the block has been mined. Hence, the "estination should encompass a `set` of activities that could be performed with the mined block. This is why sometimes the destination is also referred to as the "mode" or "activity" of a block once it has been mined. There are typically many different destinations that a block can take. An example of alternative destinations at an open pit mine is as follows:
1. A block is mined using a face shovel and sent to the processing plant (`destination: block->face shovel->plant`)
2. A block is mined using a front-end loader and sent to the processing plant (`destination: block->front-end loader-> plant`)
3. A block is mine and sent to the waste dump (`destination: block->waste dump`)

It is often the case in mining that the downstream destination decisions are required to be more granular for the material contained inside a block than the decision to mine the block itself. An example is if a bench at an open pit mine is being treated as the mining-level decision, not all material on that bench should go to the same destination. A bench would typically contain some amount of ore that would go to the plant, and some amount of waste that would go to the dump. For this reason, blocks are split up into multiple "parcels", with the mining decision being made on the block level, and the destination decision being made independently for each parcel. The parcels contained within a block are however mined in equal proportions - it would be rare to be able to access all the ore on a bench without having to mine any of the waste.

## integer terms in the linear program



## AT-format and BY-format

It’s common in the basic formulation of scheduling models to express the decision variables in terms of activities happening **at** a time period rather than **by** a time period. For example, it would be normal to state that *block x* is mined at *period y*, as opposed to *block x* is mined in some time period by the end of *period y* (including *period y*).

Nonetheless, it is actually more natural to model decisions in scheduling problems as happening **by** a time period. This modelling assumption implies (importantly) that decisions made during a mining schedule are actually cumulative, whereby a decision to mine a block in an earlier period has a cumulative impact on whether said block can be mined in later periods. 

While at first this “by-formulation” may seem unnecessary and cumbersome, it becomes clear when you analyse the structure of models used by some existing mine planning softwares that they also use a by-formulation. The most classic and simple example would be a pit optimisation problem (yes, this is not a scheduling problem), such as solved by Geovia Whittle, where there a set of blocks with economic values and precedences between different blocks. The precedences between blocks actually control the cumulative decision making nature of the problem. To mine a block on bench 3, you must have also mined its preceding blocks on benches 1 and 2. Therefore the preceding blocks must be *accumulated* before the block of interest can be extracted.

Utilising a by-formulation to model mine scheduling problems affords multiple benefits over the simpler at-formulation. Note for completeness here that using a by-formulation and at-formulation will always give the same schedule result, but are just two different variable spaces for modelling the same problem (think of an angle measured in degrees vs. radians - both are representations of the same angle but in different variable spaces). 

The first benefit is actually reducing the number of non-zero variables in the linear programming decision variable coefficient matrix (the "A" matrix). Don't let that mouthful confuse you, it is simple to show with an example that using a by-formulation can actually make modelling the finitude constraints (i.e., can only mine a block once) and precedence constraints alot easier. Lets assume we have two blocks *x1* and *x2*, with five time periods *t1*, *t2*, *t3*, *t4*, *t5*, and want to write the linear program of the precedence constraints for these blocks (see below). In terms of the decision variables we have $$x1_{t1}$$, $$x1_{t2}$$, $$x1_{t3}$$, $$x1_{t4}$$, $$x1_{t5}$$, $$x2_{t1}$$, $$x2_{t2}$$, $$x2_{t3}$$, $$x2_{t4}$$, $$x2_{t5}$$, with each binary variable tracking if block *x* is mined in that time period *t*. We also assume that *x2* is a precedent to *x1*. You can see below that using the at-formulation we must sum up and check whether *x2* has already been mined in previous periods to *x1*, whereas with the by-formulation we can just make sure for each time period that *x2* must equal to or greater than in the previous periods to *x1* (i.e., representing the cumulative nature of mining a block). Obviously for this toy example the difference is not major, but you could imagine the difference in the size of the linear program for instances with millions of blocks and tens of periods.

$$AT-formulation:$$

$$ precedence~constraint~for~period~1:~x1_{t1} \leq x2_{t1}  $$

$$ precedence~constraint~for~period~2:~x1_{t2} \leq x2_{t1} + x2_{t2} $$

$$ precedence~constraint~for~period~3:~x1_{t3} \leq x2_{t1} + x2_{t2} + x2_{t3} $$

$$ precedence~constraint~for~period~4:~x1_{t4} \leq x2_{t1} + x2_{t2} + x2_{t3} + x2_{t4} $$

$$ precedence~constraint~for~period~5:~x1_{t5} \leq x2_{t1} + x2_{t2} + x2_{t3} + x2_{t4} + x2_{t5} $$

$$BY-formulation:$$

$$~precedence~constraint~for~period~1:~x1_{t1} \leq x2_{t1} $$

$$~precedence~constraint~for~period~2:~x1_{t2} \leq x2_{t2} $$

$$~precedence~constraint~for~period~3:~x1_{t3} \leq x2_{t3} $$

$$~precedence~constraint~for~period~4:~x1_{t4} \leq x2_{t4} $$

$$~precedence~constraint~for~period~5:~x1_{t5} \leq x2_{t5} $$

The second benefit of using the by-formulation is bit more difficult to explain and is beyond the scope of this documentation, but by modelling decisions to mine a block in different time periods, and send it to different destinations as a cumulative set of decisions, this allows us to represent these cumulative precedence relationships using a directed acyclic graph (DAG), similar to precedence arcs used in pseudoflow for the ultimate pit problem (interestingly, the ultimate pit problem is actually a single-period scheduling problem where there are no constraints). This DAG structure of the by-formulation allows us to apply specialised algorithms, such as the [Bienstock-Zuckerberg algorithm]({{ site.url }}/assets/papers/Bienstock_Zuckerberg_2009.pdf){:target="_blank"}, to more efficiently solve the mixed-integer linear program. If you are interested in reading more about this special structure created in the linear program by utilising the by-formulation, please refer to the following non-exhaustive list of references:
* [A maximum flow formulation of <br>a multi-period open-pit mining problem]({{ site.url }}/assets/papers/Amankwah_etal_2014.pdf){:target="_blank"}
* [Solving LP Relaxations of Large-<br>Scale Precedence Constrained Problems]({{ site.url }}/assets/papers/Bienstock_Zuckerberg_2009.pdf){:target="_blank"}
* [Solving the mixed-integer linear programming <br>problem for mine production scheduling with <br>stockpiling under multi-element geological <br>uncertainty]({{ site.url }}/assets/papers/Brika_etal_2019.pdf){:target="_blank"}

The final topic to discuss relating to at-formulations vs. by-formulations is how we can actually transpose linear program inputs and results between these two variable spaces, as people will still want the schedule results reported in terms of blocks and parcels being mined *at* a time period.

## stockpiling

Stockpiling requires a special mention, as it is one of the most common aspects of mine planning that is run up the flag pole as a weakness of using linear programming for mine scheduling, and why linear programming should not be used. In theory, stockpiling should just be able to be treated as another destination for a parcel once it has been extracted. A parcel should be able to be stored for a certain number of periods, and then at some later date be reclaimed and processed. This method of allowing each parcel to be stockpiled individually without any mixing between parcels is a simple linear problem, and can be easily incorporated into a linear programming model. 

The problem arises however, when we make the assumption that stockpiles are completely mixed and all material placed on a stockpile becomes homogeneous. This is a fairly standard assumption in mine planning contexts (especially strategic planning), and is considered to be a conservative assumption. In a production envvironment at an operating mine, it is typically expected that once a parcel of ore is placed on a stockpile, it won't be able to be selectively reclaimed later. When asked about stockpiles, mine planners will typically say "there is a stockpile that contains 1Mt of ore at 1.8 g/t Au" (i.e., they're reporting the mixed average grade). They <u>won't</u> typically say "there is a stockpile that contains 5,134 parcels of ore, and the first parcel is 2.3 g/t, the second parcel is 1.7 g/t, the third parcel is 2.0 g/t, etc...".  

Assuming all parcels placed on a stockpile are mixed introduces a non-linear term into the mathematical model. This obviously cannot be solved simply using standard linear programming techniques. There are numerous methods that are used to either simplify and overcome the non-linear stockpile mixing assumption:
1. **Don't consider reclaiming as part of the optimisation**: This is probably the most simple method and basically doesn't optimise stockpile movements as part of the schedule. This method also only works with a window stepping time horizon scheduling method. If a problem is tried to be solved globally, ore can be stockpiled, but can never be reclaimed. Each stockpile is treated as a destination during each period of the schedule. When the schedule is solved for that period and moves to the next period, previously stockpiled material becomes a new source. However, with this method the optimiser cannot see that material that is stockpiled in this period is able to be reclaimed in later periods. This is the method utilised by [Minemax Scheduler](https://www.minemax.com/products/scheduler/).
2. **Assume each stockpile has a fixed average grade**: 
3. **Piece-wise linear approximation of the quadratic mixing constraints**: 
4. **Specialised branching method**: This method uses a specialised version of the branch-and-bound method

