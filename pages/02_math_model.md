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

$$ p \in P : $$ parcels contained within block b; $$~1,...,P $$

$$ t \in T : $$ time periods; $$~1,...,T $$

$$ d \in D : $$ destinations; $$~1,...,D $$

$$ s \in S : $$ stockpiles; $$~1,...,S $$

$$ q \in Q : $$ side constraints; $$~1,...,Q $$

$$ c \in C : $$ capital expenditure options; $$~1,...,C $$

$$ \hat{q}_c \in \hat{Q}_c : $$ side constraints expanded by purchasing capital option c; $$~1,...,\hat{Q}_c ; \hat{Q}_c \subseteq Q $$

$$ f \in F : $$ fixed costs; $$~1,...,F $$

$$ \hat{p}_f \in \hat{P}_f : $$ parcels that can trigger fixed cost f; $$~1,...,\hat{P}_f ; \hat{P}_f \subseteq P $$

$$ \hat{d}_f \in \hat{D}_f : $$ destinations that can trigger fixed cost f; $$~1,...,\hat{D}_f ; \hat{D}_f \subseteq D $$

## decision variables (by-format)

$$x_{\mathrm{bt}} : $$ 1 if block `b` has been mined by period `t`, otherwise 0.

$$y_{\mathrm{bptd}} : $$ fraction of parcel `p` contained in block `b` mined by period `t` and sent to destination `d`. 

$$z_{\mathrm{bpts}} : $$ fraction of parcel `p` contained in block `b` mined by period `t` and sent to stockpile `s`. 

$$z_{\mathrm{sptd}} : $$ fraction of parcel `p` contained in stockpile `s` reclaimed by period `t` and sent to destination `d`. 

$$u_{\mathrm{c\hat{q}}} : $$ 1 if capital decision `c` has been purchased to expand the capacity of a set of constraints $$\hat{q}$$, otherwise 0.

$$v_{\mathrm{f\hat{p}_f\hat{d}_f}} : $$ 1 if fixed cost has been paid to allow subset of parcels $$\hat{p}_f$$ contained in blocks `b` mined by period `t` to be sent to subset of destinations $$\hat{d}_f$$, otherwise 0.

precedence variables (binary) : $$ x_{\mathrm{bt}}\in\{0,1\} $$

decision variables (continuous) : $$ y_{\mathrm{bptd}}\in[ \,0,1] $$

decision variables (continuous) : $$ z_{\mathrm{bpts}}\in[ \,0,1] $$

decision variables (continuous) : $$ z_{\mathrm{sptd}}\in[ \,0,1] $$

capital decision variables (binary) : $$ u_{\mathrm{c\hat{q}}} $$

fixed cost variables (binary) : $$ v_{\mathrm{f\hat{p}_f\hat{d}_f}}\in\{0,1\} $$

## additional variables (by-format)

$$p_{\mathrm{bptd}}^y : $$ objective value of parcel `p` contained in block `b` mined by period `t` and sent to destination `d`. 

$$p_{\mathrm{bpts}}^z : $$ objective value of parcel `p` contained in block `b` mined by period `t` and sent to stockpile `s`. 

$$p_{\mathrm{sptd}}^z : $$ objective value of parcel `p` contained in stockpile `s` reclaimed by period `t` and sent to destination `d`. 

$$p_{\mathrm{c\hat{q}}}^u : $$ objective value of capital decision `c`. 

$$p_{\mathrm{f\hat{p}_f\hat{d}_f}}^v : $$ objective value of fixed cost `f`. 

note that $$p$$ is usually the discounted value of making this decision, however it can be any value (recovered product, etc...)

## objective function (by-format)

$$ max~\displaystyle\sum_{b\in B} \displaystyle\sum_{p\in P} \displaystyle\sum_{t\in T} \displaystyle\sum_{d\in D} p_{\mathrm{bptd}}^y y_{\mathrm{bptd}} + \\~~~~~~~~ \displaystyle\sum_{b\in B} \displaystyle\sum_{p\in P} \displaystyle\sum_{t\in T} \displaystyle\sum_{s\in S} p_{\mathrm{bpts}}^z z_{\mathrm{bpts}} + \\~~~~~~~~ \displaystyle\sum_{s\in S} \displaystyle\sum_{p\in P} \displaystyle\sum_{t\in T} \displaystyle\sum_{d\in D} p_{\mathrm{sptd}}^z z_{\mathrm{sptd}} + \\~~~~~~~~ \displaystyle\sum_{c\in C} p_{\mathrm{c\hat{q}}}^u + \\~~~~~~~~ \displaystyle\sum_{f\in F} p_{\mathrm{f\hat{p}_f\hat{d}_f}}^v $$

## constraints (by-format)

**(1) Mine Each Block Only Once (finitude)**

$$For~b \in B,~t \in \{ 2,...,T \}: $$

$$ x_{\mathrm{bt-1}} \leq x_{\mathrm{bt}}~which~is~equivalent~to:~x_{\mathrm{bt-1}} - x_{\mathrm{bt}} \leq 0 $$

**(2) Mine Each Parcel Only Once (finitude)**

$$For~b \in B,~p \in P,~t \in T,~d \in \{ 1,...,D-1 \}: $$

$$ y_{\mathrm{bptd}} \leq y_{\mathrm{bptd+1}}~which~is~equivalent~to:~y_{\mathrm{bptd}} - y_{\mathrm{bptd+1}} \leq 0 $$

$$For~b \in B,~p \in P,~t \in \{ 1,...,T-1 \},~d = D: $$

$$ y_{\mathrm{bptd}} \leq y_{\mathrm{bp+1td}}~which~is~equivalent~to:~y_{\mathrm{bptd}} - y_{\mathrm{bp+1td}} \leq 0 $$

**(3) Precedences**

$$ \displaystyle\sum_{\tau=1}^{t} \displaystyle\sum_{d=1}^{D} y_{\mathrm{b \tau d}} \leq x_{\mathrm{\hat{b}t}}~which~is~equivalent~to:~$$
$$ \displaystyle\sum_{\tau=1}^{t} \displaystyle\sum_{d=1}^{D} y_{\mathrm{b \tau d}} - x_{\mathrm{\hat{b}t}} \leq 0 $$

**(4) Linking Constraint**

$$ x_{\mathrm{bt}} \leq \displaystyle\sum_{\tau=1}^{t} \displaystyle\sum_{d=1}^{D} y_{\mathrm{b \tau d}}~which~is~equivalent~to:~$$
$$ \displaystyle\sum_{\tau=1}^{t} \displaystyle\sum_{d=1}^{D} y_{\mathrm{b \tau d}} - x_{\mathrm{bt}} \geq 0 $$

**(6) General Side Constraints**

* Attribute Sum
* Attribute Average
* Mine Block After Period
* Mine Block Before Period

