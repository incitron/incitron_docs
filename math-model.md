---
layout: default
title: math model
nav_order: 3
has_children: false
---

{% include mathjax.html %}

# Mathematical Model
----------

This page describes the mathematical representation of the MILP model that is solved by the incitron engine.
This is a Precedence Constrained Production Scheduling Problem (PCPSP).

## Decision Variables
------------------

$$ x_{\mathrm{bt}} = whether~block~\textbf{b}~has~been~mined~BY~period~\textbf{t},~otherwise~0. $$

$$ y_{\mathrm{btd}} = fraction~of~block~\textbf{b}~mined~BY~period~\textbf{t}~and~sent~to~destination~\textbf{d}. $$

$$ precedence~variables~(binary)~:~x_{\mathrm{bt}} $$

$$ x_{\mathrm{bt}}\in\{0,1\} $$

$$ decision~variables~(continuous)~:~y_{\mathrm{btd}} $$

$$ y_{\mathrm{btd}}\in[ \,0,1] $$

-----------------

## Additional Variables
--------------------

$$ p_{\mathrm{btd}} = discounted~value~of~block~\textbf{b}~if~it~is~mined~BY~time~\textbf{t}~and~sent~to~destination~\textbf{d}. $$

---------------

## Indices & Sets
--------------

$$ b \in B : blocks;~1,...,B $$

$$ \hat{b} \in \hat{B}_b :~blocks~that~must~be~mined~directly~before~block~\textbf{b};~1,...,\hat{B}_b $$

$$ t \in T : time~periods;~1,...,T $$

$$ d \in D : destinations;~1,...,D $$

----------------

## Objective Function
------------------

$$ max~\displaystyle\sum_{b=1}^{B} \displaystyle\sum_{t=1}^{T} \displaystyle\sum_{d=1}^{D} p_{\mathrm{btd}} y_{\mathrm{btd}} $$

---------------

## Constraints
-----------

**(1) Mine Each Block Only Once**

$$ \displaystyle\sum_{t=1}^{T} \displaystyle\sum_{d=1}^{D} y_{\mathrm{btd}} \leq 1 $$

**(2) BY Variable Sequentiality**

$$ x_{\mathrm{bt-1}} \leq x_{\mathrm{bt}}~which~is~equivalent~to:~x_{\mathrm{bt}} - x_{\mathrm{bt-1}} \geq 0 $$

**(3) BY Variable Precedences (could potentially be redundant)**

$$ x_{\mathrm{bt}} \leq x_{\mathrm{\hat{b}t}}~which~is~equivalent~to:~x_{\mathrm{bt}} - x_{\mathrm{\hat{b}t}} \leq 0 $$

**(4) Decision Variable Precedences**

$$ \displaystyle\sum_{\tau=1}^{t} \displaystyle\sum_{d=1}^{D} y_{\mathrm{b \tau d}} \leq x_{\mathrm{\hat{b}t}} which is equivalent to: $$
$$ \displaystyle\sum_{\tau=1}^{t} \displaystyle\sum_{d=1}^{D} y_{\mathrm{b \tau d}} - x_{\mathrm{\hat{b}t}} \leq 0 $$

**(5) Linking Constraint**

$$ x_{\mathrm{bt}} \leq \displaystyle\sum_{\tau=1}^{t} \displaystyle\sum_{d=1}^{D} y_{\mathrm{b \tau d}} which is equivalent to: $$
$$ \displaystyle\sum_{\tau=1}^{t} \displaystyle\sum_{d=1}^{D} y_{\mathrm{b \tau d}} - x_{\mathrm{bt}} \geq 0 $$

**(6) General Side Constraints**

* Attribute Sum
* Attribute Average
* Mine Block After Period
* Mine Block Before Period

----------------

