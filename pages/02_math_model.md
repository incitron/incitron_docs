---
layout: default
title: 2. math model
nav_order: 2
has_children: false
---

{% include mathjax.html %}

# mathematical model
----------

This page describes the mathematical representation of the MILP model that is solved by the incitron engine.
This is a Precedence Constrained Production Scheduling Problem (PCPSP) with stockpiling.

## indices & sets
--------------

$$ b \in B : blocks; $$~1,...,B $$

$$ \hat{b} \in \hat{B}_b : $$  blocks that must be mined directly before block b; $$~1,...,\hat{B}_b $$

$$ t \in T : time periods; $$~1,...,T $$

$$ d \in D : destinations; $$~1,...,D $$

----------------

## decision variables
------------------

$$x_{\mathrm{bt}} = $$ 1 if block b has been mined BY period t, otherwise 0.

$$y_{\mathrm{btd}} = $$ fraction of block b mined BY period t and sent to destination d. 

precedence variables (binary) : $$x_{\mathrm{bt}}$$

$$ x_{\mathrm{bt}}\in\{0,1\} $$

decision variables (continuous) : $$y_{\mathrm{btd}}$$

$$ y_{\mathrm{btd}}\in[ \,0,1] $$

-----------------

## additional variables
--------------------

$$p_{\mathrm{btd}} = $$ objective value of block b if it is mined BY time t and sent to destination d. 

note that $$p_{\mathrm{btd}}$$ is usually the discounted value of making this decision, however it can be any value (recovered product, etc...)

---------------

## objective function
------------------

$$ max~\displaystyle\sum_{b=1}^{B} \displaystyle\sum_{t=1}^{T} \displaystyle\sum_{d=1}^{D} p_{\mathrm{btd}} y_{\mathrm{btd}} $$

---------------

## constraints
-----------

**(1) Mine Each Block Only Once**

$$ \displaystyle\sum_{t=1}^{T} \displaystyle\sum_{d=1}^{D} y_{\mathrm{btd}} \leq 1 $$

**(2) BY Variable Sequentiality**

$$ x_{\mathrm{bt-1}} \leq x_{\mathrm{bt}}~which~is~equivalent~to:~x_{\mathrm{bt}} - x_{\mathrm{bt-1}} \geq 0 $$

**(3) BY Variable Precedences (could potentially be redundant)**

$$ x_{\mathrm{bt}} \leq x_{\mathrm{\hat{b}t}}~which~is~equivalent~to:~x_{\mathrm{bt}} - x_{\mathrm{\hat{b}t}} \leq 0 $$

**(4) Decision Variable Precedences**

$$ \displaystyle\sum_{\tau=1}^{t} \displaystyle\sum_{d=1}^{D} y_{\mathrm{b \tau d}} \leq x_{\mathrm{\hat{b}t}}~which~is~equivalent~to:~$$
$$ \displaystyle\sum_{\tau=1}^{t} \displaystyle\sum_{d=1}^{D} y_{\mathrm{b \tau d}} - x_{\mathrm{\hat{b}t}} \leq 0 $$

**(5) Linking Constraint**

$$ x_{\mathrm{bt}} \leq \displaystyle\sum_{\tau=1}^{t} \displaystyle\sum_{d=1}^{D} y_{\mathrm{b \tau d}}~which~is~equivalent~to:~$$
$$ \displaystyle\sum_{\tau=1}^{t} \displaystyle\sum_{d=1}^{D} y_{\mathrm{b \tau d}} - x_{\mathrm{bt}} \geq 0 $$

**(6) General Side Constraints**

* Attribute Sum
* Attribute Average
* Mine Block After Period
* Mine Block Before Period

----------------

