# AT-Format Math Model

**Table of contents**
* [Indices and sets](#indices-and-sets)
* [Decision variables](#decision-variables-at-format)
* [Additional variables](#additional-variables-at-format)
* [Objective function](#objective-function-at-format)
* [Constraints](#constraints-at-format):
  * [Mine Each Block Only Once](#1-mine-each-block-only-once-finitude-constraint)
  * [Mine Each Parcel Only Once](#2-mine-each-parcel-only-once-finitude-constraint)
  * [Precedence Constraint](#3-precedence-constraint)
  * [Linking Constraint](#4-linking-constraint)
  * [Equal-Parcel Constraint](#5-equal-parcel-constraint)
  * [Stockpile Flow-Balancing Constraint](#6-stockpile-flow-balancing-constraint)
  * [Stockpile Mixing Constraint](#7-stockpile-mixing-constraint-equal-out-fractions)
  * [General Side Constraints](#8-general-side-constraints)

## Indices and sets

\\( b \in B \\) : blocks; \\(  \\ 1,...,B \\)

\\( \hat{b} \in \hat{B}_b \\) : blocks that must be mined directly before block **b**; \\(  \\ 1,...,\hat{B}_b \\)

\\( p \in P_b \\) : parcels contained within block **b**; \\(  \\ 1,...,P_b \\)

\\( t \in T \\) : time periods; \\(  \\ 1,...,T \\)

\\( d \in D \\) : destinations; \\(  \\ 1,...,D \\)

\\( s \in S \\) : stockpiles; \\(  \\ 1,...,S \\)

\\( q \in Q \\) : side constraints; \\(  \\ 1,...,Q \\)

\\( c \in C \\) : capital expenditure options; \\(  \\ 1,...,C \\)

\\( \hat{q}_c \in \hat{Q}_c \\) : side constraints expanded by purchasing capital option **c**; \\(  \\ 1,...,\hat{Q}_c ;\\ \hat{Q}_c \subseteq Q \\)

\\( f \in F \\) : fixed costs; \\(  \\ 1,...,F \\)

\\( \hat{p}_f \in \hat{P}\_{bf} \\) : parcels that can trigger fixed cost **f**; \\(  \\ 1,...,\hat{P}\_{bf} ;\\ \hat{P}\_{bf} \subseteq P_b \\)

\\( \hat{d}_f \in \hat{D}_f \\) : destinations that can trigger fixed cost **f**; \\(  \\ 1,...,\hat{D}_f ;\\ \hat{D}_f \subseteq D \\)

## Decision variables (AT-format)

\\(x_{\mathrm{bt}} : \\) 1 if block **b** is been completely <u>at</u> period **t**, otherwise 0.

\\(y_{\mathrm{bptd}} : \\) fraction of parcel **p** contained in block **b** mined <u>at</u> period **t** and sent to destination **d**.

\\(z_{\mathrm{bpts}} : \\) fraction of parcel **p** contained in block **b** mined <u>at</u> period **t** and sent to stockpile **s**. 

\\(z_{\mathrm{sptd}} : \\) fraction of parcel **p** contained in stockpile **s** reclaimed <u>at</u> period **t** and sent to destination **d**. 

\\(f_{\mathrm{st}} : \\) relative proportion of parcels from the stockpile **s** reclaimed in time period **t** (i.e., "out-fraction").

\\(u_{\mathrm{c\hat{q}}} : \\) 1 if capital decision **c** is purchased to expand the capacity of a set of constraints \\( \hat{q} \\), otherwise 0.

\\(v_{\mathrm{f\hat{p}_f\hat{d}_f}} : \\) 1 if fixed cost is paid to allow subset of parcels \\( \hat{p}_f \\) contained in blocks **b** mined <u>at</u> period **t** to be sent to subset of destinations \\( \hat{d}_f \\), otherwise 0.

precedence variables (binary) : \\( x_{\mathrm{bt}}\in\{0,1\} \\)

decision variables (continuous) : \\( y_{\mathrm{bptd}}\in[0,1] \\)

stockpiling variables (continuous) : \\( z_{\mathrm{bpts}}\in[0,1] \\)

reclaiming variables (continuous) : \\( z_{\mathrm{sptd}}\in[0,1] \\)

reclaiming proportions (continous) : \\( f_{\mathrm{st}}\in[0,1] \\)

capital decision variables (binary) : \\( u_{\mathrm{c\hat{q}}}\in\{0,1\} \\)

fixed cost variables (binary) : \\( v_{\mathrm{f\hat{p}_f\hat{d}_f}}\in\{0,1\} \\)

## Additional variables (AT-format)

\\( p_{\mathrm{bptd}}^y : \\) objective value of parcel **p** contained in block **b** mined <u>at</u> period **t** and sent to destination **d**. 

\\( p_{\mathrm{bpts}}^z : \\) objective value of parcel **p** contained in block **b** mined <u>at</u> period **t** and sent to stockpile **s**. 

\\( p_{\mathrm{sptd}}^z : \\) objective value of parcel **p** contained in stockpile **s** reclaimed <u>at</u> period **t** and sent to destination **d**. 

\\( p_{\mathrm{c\hat{q}}}^u : \\) objective value of capital decision **c**. 

\\( p_{\mathrm{f\hat{p}_f\hat{d}_f}}^v : \\) objective value of fixed cost **f**. 

note that \\( p \\) is usually the discounted value of making this decision, however it can be any value (recovered product, etc...)

## Objective function (AT-format)

\\( max~\displaystyle\sum_{b\in B} \displaystyle\sum_{p\in P_b} \displaystyle\sum_{t\in T} \displaystyle\sum_{d\in D} p_{\mathrm{bptd}}^y y_{\mathrm{bptd}} \\ +
\\\\~~~~~~~~~ \displaystyle\sum_{b\in B} \displaystyle\sum_{p\in P_b} \displaystyle\sum_{t\in T} \displaystyle\sum_{s\in S} p_{\mathrm{bpts}}^z z_{\mathrm{bpts}} \\ + 
\\\\~~~~~~~~~ \displaystyle\sum_{s\in S} \displaystyle\sum_{p\in P_b} \displaystyle\sum_{t\in T} \displaystyle\sum_{d\in D} p_{\mathrm{sptd}}^z z_{\mathrm{sptd}} \\ + 
\\\\~~~~~~~~~ \displaystyle\sum_{c\in C} p_{\mathrm{c\hat{q}}}^u \\ + 
\\\\~~~~~~~~~ \displaystyle\sum_{f\in F} p_{\mathrm{f\hat{p}_f\hat{d}_f}}^v \\)

## Constraints (AT-format)

### 1) Mine each block only once (finitude constraint)

\\( For \\ b \in B, \\ t \in \{ 2,...,T \}: \\)

\\( \displaystyle\sum_{\tau=1}^{t-1} x_{\mathrm{b\tau}} \leq x_{\mathrm{bt}}\\ \Longleftrightarrow \\ \displaystyle\sum_{\tau=1}^{t-1} x_{\mathrm{b\tau}} - x_{\mathrm{bt}} \leq 0 \\)

### 2) Mine each parcel only once (finitude constraint)

Note here we treat sending parcels to stockpiles as a seperate set of variables (with the last stockpile S being equivalent to the last destination index), but realistically each stockpile could just be considered as another "destination".

\\( For \\ b \in B, \\ p \in P_b, \\ t \in \{ 2,...,T \}: \\)

\\( \displaystyle\sum_{d\in D} \displaystyle\sum_{\tau=1}^{t-1} y_{\mathrm{b p \tau d}} + \displaystyle\sum_{s\in S} \displaystyle\sum_{\tau=1}^{t-1} z_{\mathrm{b p \tau s}} \leq y_{\mathrm{bptd}} + z_{\mathrm{bpts}}\\ \Longleftrightarrow \\ \displaystyle\sum_{d\in D} \displaystyle\sum_{\tau=1}^{t-1} y_{\mathrm{b p \tau d}} + \displaystyle\sum_{s\in S} \displaystyle\sum_{\tau=1}^{t-1} z_{\mathrm{b p \tau s}} - y_{\mathrm{bptd}} - z_{\mathrm{bpts}} \leq 0 \\)

### 3) Precedence constraint

This constraint ensures that all the preceeding blocks have been mined before the parcels in the current block can be started.

\\( For \\ b \in B, \\ p \in P_b, \\ \hat{b} \in \hat{B}_b, \\ t \in T, \\ d \in D, \\ s \in S: \\)

\\( z_{\mathrm{bptS}} \leq x_{\mathrm{\hat{b}t}}\\ \Longleftrightarrow \\ z_{\mathrm{bptS}} - x_{\mathrm{\hat{b}t}} \leq 0 \\)

### 4) Linking constraint

This constraint links the block with the parcels contained within the block (i.e., once all the parcels are completely mined within a block, said block is allowed to take a value of 1).

\\( For \\ b \in B, \\ p \in P_b, \\ t \in T, \\ s = S: \\)

\\( x_{\mathrm{bt}} \leq z_{\mathrm{bptS}}\\ \Longleftrightarrow \\ x_{\mathrm{bt}} - z_{\mathrm{bptS}} \leq 0 \\)

### 5) Equal-parcel constraint

This constraint ensures that parcels in a block are mined in equal proportions (i.e., don't just mine all the ore parcels first and leave all the waste).

\\( For \\ b \in B, \\ p \in \{ 2,...,P_b \}, \\ t \in T, \\ s = S: \\)

\\( z_{\mathrm{bp-1tS}} \leq z_{\mathrm{bptS}}\\ \Longleftrightarrow \\ z_{\mathrm{bp-1tS}} - z_{\mathrm{bptS}} \leq 0 \\)

### 6) Stockpile flow-balancing constraint

This constraints ensures that the amount of a parcel that leaves a stockpile must be the same or less than the amount of said parcel that has entered the stockpile in previous periods.

\\( For \\ b \in B, \\ p \in P_b, \\ t \in T, \\ s \in S, \\ d = D: \\)

\\( z_{\mathrm{sptD}} \leq z_{\mathrm{bpts}}\\ \Longleftrightarrow \\ z_{\mathrm{sptD}} - z_{\mathrm{bpts}} \leq 0 \\)

### 7) Stockpile mixing constraint (equal out-fractions)

This constraint ensures that stockpiles are mixed (i.e., the proportion of parcels reclaimed from each stockpile in each period must be the same). Note that in the first period we have to ensure any pre-existing stockpile balances are reclaimed equally. Stockpile mixing is a non-linear constraint, but can be solved with a MILP branch-and-bound algorithm framework, as described by [Bley et. al., 2012](../assets/papers/Bley_etal_2012.pdf).

\\( For \\ b \in B, \\ p \in P_b, \\ t = 1, \\ s \in S, \\ d = D: \\)

\\( z_{\mathrm{sp1D}} = f_{\mathrm{s1}} \\)

\\( For \\ b \in B, \\ p \in P_b, \\ t \in \{ 2,...,T \}, \\ s \in S, \\ d = D: \\)

\\( \frac{z_{\mathrm{sptD}} - z_{\mathrm{spt-1D}}}{z_{\mathrm{bpt-1s}} - z_{\mathrm{spt-1D}}} = f_{\mathrm{st}} \\)

**Solving non-linear stockpiling mixing constraints:**

A special branching scheme is used to force the maximum violation of the nonlinear constraints arbitrarily close to zero, as described by [Bley et. al., 2012](../assets/papers/Bley_etal_2012.pdf). The special branching method only adds linear constraints at each node in the branch-and-bound tree, so is practically efficient to solve using modern MILP solvers.

Assuming we have a valid linear relaxation solution (fractional) that violates the stockpile mixing constraint for a certain time period. Then there exists two parcels \\( i,j \in P \\) with different out-fractions (i.e., different values for \\( f_{\mathrm{st}} \\)). Thus there must be a ratio:

\\( \frac{z_{\mathrm{sitD}} - z_{\mathrm{sit-1D}}}{z_{\mathrm{bit-1s}} - z_{\mathrm{sit-1D}}} \lt \phi \lt \frac{z_{\mathrm{sjtD}} - z_{\mathrm{sjt-1D}}}{z_{\mathrm{bjt-1s}} - z_{\mathrm{sjt-1D}}} \\)

This inequality creates two branches at that node in the B&B tree:

Force the out-fractions of all parcels from a stockpile in a period to be <u>no more</u> than \\( \phi \\):

\\( z_{\mathrm{sptD}} + (\phi - 1) z_{\mathrm{spt-1D}} \leq  \phi z_{\mathrm{bpt-1s}}\\ \Longleftrightarrow \\  z_{\mathrm{sptD}} + (\phi - 1) z_{\mathrm{spt-1D}} - \phi z_{\mathrm{bpt-1s}} \leq 0 \\)

Force the out-fractions of all parcels from a stockpile in a period to be <u>no less</u> than \\( \phi \\):

\\( z_{\mathrm{sptD}} + (\phi - 1) z_{\mathrm{spt-1D}} \geq  \phi z_{\mathrm{bpt-1s}}\\ \Longleftrightarrow \\  z_{\mathrm{sptD}} + (\phi - 1) z_{\mathrm{spt-1D}} - \phi z_{\mathrm{bpt-1s}} \geq 0 \\)

For simplicity we usually choose a value of \\( \phi \\) that is the mean of the minimum and maximum out-fractions from a stockpile in a period.

### 8) General side constraints

* attribute sum
* attribute average
* mine block after period
* don't mine block after period
* mine block before period
* don't mine block before period

