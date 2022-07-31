---
layout: default
title: 2. math model
nav_order: 2
has_children: false
---

{% include mathjax.html %}

# mathematical model

This page describes the mathematical representation of the `MILP` model that is solved by the incitron engine. This is a `Precedence Constrained Production Scheduling Problem (PCPSP)` with stockpiling. Please take note of the special use of completing mining blocks and parcels `by` a time period, rather than `at` a time period.

## indices & sets

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

**(1) Mine Each Block Only Once (finitude constraint)**

$$For~b \in B,~t \in \{ 2,...,T \}: $$

$$ x_{\mathrm{bt-1}} \leq x_{\mathrm{bt}}~which~is~equivalent~to:~x_{\mathrm{bt-1}} - x_{\mathrm{bt}} \leq 0 $$

**(2) Mine Each Parcel Only Once (finitude constraint)**

Note here we treat sending parcels to stockpiles as a seperate set of constraints (with the last stockpile S being equivalent to the last destination index), but realistically each stockpile could just be considered as another "destination".

$$For~b \in B,~p \in P_b,~t \in T,~d \in \{ 1,...,D-1 \}: $$

$$ y_{\mathrm{bptd}} \leq y_{\mathrm{bptd+1}}~which~is~equivalent~to:~y_{\mathrm{bptd}} - y_{\mathrm{bptd+1}} \leq 0 $$

$$For~b \in B,~p \in P_b,~t \in T,~d = D,~s = 1: $$

$$ y_{\mathrm{bptD}} \leq z_{\mathrm{bpts}}~which~is~equivalent~to:~y_{\mathrm{bptD}} - z_{\mathrm{bpts}} \leq 0 $$

$$For~b \in B,~p \in P_b,~t \in T,~s \in \{ s,...,S-1 \}: $$

$$ z_{\mathrm{bpts}} \leq z_{\mathrm{bpts+1}}~which~is~equivalent~to:~z_{\mathrm{bpts}} - z_{\mathrm{bpts+1}} \leq 0 $$

$$For~b \in B,~p \in P_b,~t \in \{ 1,...,T-1 \},~s = S: $$

$$ z_{\mathrm{bptS}} \leq z_{\mathrm{bp+1tS}}~which~is~equivalent~to:~z_{\mathrm{bptS}} - z_{\mathrm{bp+1tS}} \leq 0 $$

**(3) Precedence Constraint**

This constraint ensures that all the preceeding blocks have been mined before the parcels in the current block can be started.

$$For~b \in B,~p \in P_b,~\hat{b} \in \hat{B}_b,~t \in T,~s = S: $$

$$ z_{\mathrm{bptS}} \leq x_{\mathrm{\hat{b}t}}~which~is~equivalent~to:~z_{\mathrm{bptS}} - x_{\mathrm{\hat{b}t}} \leq 0 $$

**(4) Linking Constraint**

This constraint links the block with the parcels contained within the block (i.e., once all the parcels are completely mined within a block, said block is allowed to take a value of 1).

$$For~b \in B,~p \in P_b,~t \in T,~s = S: $$

$$ x_{\mathrm{bt}} \leq z_{\mathrm{bptS}}~which~is~equivalent~to:~x_{\mathrm{bt}} - z_{\mathrm{bptS}} \leq 0 $$

**(5) Equal-Parcel Constraint**

This constraint ensures that parcels in a block are mined in equal proportions (i.e., don't just mine all the ore parcels first and leave all the waste).

$$For~b \in B,~p \in \{ 2,...,P_b \},~t \in T,~s = S: $$

$$ z_{\mathrm{bp-1tS}} \leq z_{\mathrm{bptS}}~which~is~equivalent~to:~z_{\mathrm{bp-1tS}} - z_{\mathrm{bptS}} \leq 0 $$

**(6) Stockpile Flow-Balancing Constraint**

This constraints ensures that the amount of a parcel that leaves a stockpile must be the same or less than the amount of said parcel that has entered the stockpile in previous periods.

$$For~b \in B,~p \in P_b,~t \in T,~s \in S,~d = D: $$

$$ z_{\mathrm{sptD}} \leq z_{\mathrm{bpts}}~which~is~equivalent~to:~z_{\mathrm{sptD}} - z_{\mathrm{bpts}} \leq 0 $$

**(7) Stockpile Mixing Constraint (equal out-fractions)**

This constraint ensures that stockpiles are mixed (i.e., the proportion of parcels reclaimed from each stockpile in each period must be the same). Note that in the first period we have to ensure any pre-existing stockpile balances are reclaimed equally. Note that stockpile mixing is a non-linear constraint, but can be solved, as described by [Bley et. al., 2012]({{ site.url }}/assets/papers/Bley_etal_2012.pdf){:target="_blank"}.

$$For~b \in B,~p \in P_b,~t = 1,~s \in S,~d = D: $$

$$ z_{\mathrm{sp1D}} = f_{\mathrm{s1}} $$

$$For~b \in B,~p \in P_b,~t \in \{ 2,...,T \},~s \in S,~d = D: $$

$$ \frac{z_{\mathrm{sptD}} - z_{\mathrm{spt-1D}}}{z_{\mathrm{bpt-1s}} - z_{\mathrm{spt-1D}}} = f_{\mathrm{st}} $$

Discretised out-fractions:

A priori discretisation of the out-fractions $$ f_{\mathrm{st}} $$ and to produce a piece-wise linear outer approximation of the non-linear mixing constraints.

**(8) General Side Constraints**

* attribute sum
* attribute average
* mine block after period
* don't mine block after period
* mine block before period
* don't mine block before period

# additional notes

This section contains some additional notes regarding the mathematical model behind incitron.
Specific topics include:
* [definition of blocks, parcels and destinations](#blocks-parcels-destinations)
* [at-format & by-format](#at-format-by-format)
* [stockpiling](#stockpiling)

## blocks, parcels, destinations

Mine planning is a classic example of the Precedence Constrained Production Scheduling Problem (PCPSP), where there are a set of activities (or "jobs") that must be scheduled to occur over a number of periods, with precedences between the activities. Further, each activity can have a number of different methods (or "ways", "modes") that it can be completed, and we must also choose the optimal method for completing said activity.

Within mine planning, the most common activity that must be scheduled is the mining of a "block" of earth. Note here that a "block" is not restricted to meaning an SMU block within a blockmodel, or even a block in an open pit mine. A "block" can represent any physical volume of rock, including:
* an SMU block on a bench
* a mining polygon on a bench in an open pit mine
* a whole bench in an open pit mine
* a whole phase at an open pit mine
* a stope at an underground mine
* a section of development at an underground mine
* a whole underground panel of mining
* a panel of a block cave mine
* etc...

Once a block has been mined, there is a secondary decision that must be made as to what should be done with the material, typically referred to as the "destination" of the block. Again, note here that a "destination" is not just restricted to a physical destination at a mine (i.e., the waste dump, the plant, etc), but should encompass any downstream decisions that are required to be made once the block has been mined. Hence, the "destination" should encompass a `set` of activities that could be performed with the mine block. There are typically many different "destinations" that a block can take. A granular example of alternative "destinations" to cement the theory is as follows:
1. A block is mined using a face shovel and sent to the processing plant (destination: block->face shovel->plant)
2. A block is mined using a front-end loader and sent to the processing plant (destination: block->front-end loader-> plant)
3. A block is mine and sent to the waste dump (destination: block->waste dump)

## at-format & by-format

decisions are cumulative


## stockpiling



