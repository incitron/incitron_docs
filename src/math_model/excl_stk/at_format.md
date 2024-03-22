# AT-Format Math Model - Excluding Stockpiling

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
  * [General Side Constraints](#6-general-side-constraints)

## Indices and sets

\\( b \in B \\) : blocks; \\(  \\ 1,...,B \\)

\\( \hat{b} \in \hat{B}_b \\) : blocks that must be mined directly before block **b**; \\(  \\ 1,...,\hat{B}_b \\)

\\( p \in P_b \\) : parcels contained within block **b**; \\(  \\ 1,...,P_b \\)

\\( t \in T \\) : time periods; \\(  \\ 1,...,T \\)

\\( d \in D \\) : destinations; \\(  \\ 1,...,D \\)

\\( q \in Q \\) : side constraints; \\(  \\ 1,...,Q \\)

\\( c \in C \\) : capital expenditure options; \\(  \\ 1,...,C \\)

\\( \hat{q}_c \in \hat{Q}_c \\) : side constraints expanded by purchasing capital option **c**; \\(  \\ 1,...,\hat{Q}_c ;\\ \hat{Q}_c \subseteq Q \\)

\\( f \in F \\) : fixed costs; \\(  \\ 1,...,F \\)

\\( \hat{p}_f \in \hat{P}\_{bf} \\) : parcels that can trigger fixed cost **f**; \\(  \\ 1,...,\hat{P}\_{bf} ;\\ \hat{P}\_{bf} \subseteq P_b \\)

\\( \hat{d}_f \in \hat{D}_f \\) : destinations that can trigger fixed cost **f**; \\(  \\ 1,...,\hat{D}_f ;\\ \hat{D}_f \subseteq D \\)

## Decision variables (AT-format)

\\(x_{\mathrm{bt}} : \\) 1 if block **b** is been completely <u>at</u> period **t**, otherwise 0.

\\(y_{\mathrm{bptd}} : \\) fraction of parcel **p** contained in block **b** mined <u>at</u> period **t** and sent to destination **d**.

\\(u_{\mathrm{c\hat{q}}} : \\) 1 if capital decision **c** is purchased to expand the capacity of a set of constraints \\( \hat{q} \\), otherwise 0.

\\(v_{\mathrm{f\hat{p}_f\hat{d}_f}} : \\) 1 if fixed cost is paid to allow subset of parcels \\( \hat{p}_f \\) contained in blocks **b** mined <u>at</u> period **t** to be sent to subset of destinations \\( \hat{d}_f \\), otherwise 0.

precedence variables (binary) : \\( x_{\mathrm{bt}}\in\{0,1\} \\)

decision variables (continuous) : \\( y_{\mathrm{bptd}}\in[0,1] \\)

capital decision variables (binary) : \\( u_{\mathrm{c\hat{q}}}\in\{0,1\} \\)

fixed cost variables (binary) : \\( v_{\mathrm{f\hat{p}_f\hat{d}_f}}\in\{0,1\} \\)

## Additional variables (AT-format)

\\( p_{\mathrm{bptd}}^y : \\) objective value of parcel **p** contained in block **b** mined <u>at</u> period **t** and sent to destination **d**. 

\\( p_{\mathrm{c\hat{q}}}^u : \\) objective value of capital decision **c**. 

\\( p_{\mathrm{f\hat{p}_f\hat{d}_f}}^v : \\) objective value of fixed cost **f**. 

note that \\( p \\) is usually the discounted value of making this decision, however it can be any value (recovered product, etc...)

## Objective function (AT-format)

\\( max~\displaystyle\sum_{b\in B} \displaystyle\sum_{p\in P_b} \displaystyle\sum_{t\in T} \displaystyle\sum_{d\in D} p_{\mathrm{bptd}}^y y_{\mathrm{bptd}} \\ +
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

### 6) General side constraints

* attribute sum
* attribute average
* mine block after period
* don't mine block after period
* mine block before period
* don't mine block before period

