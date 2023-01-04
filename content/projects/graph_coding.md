+++
title = "Graph Coding"
author = ["Tze-Yang Tung"]
date = 2023-01-04
lastmod = 2023-01-04T10:43:14+00:00
tags = ["Projects"]
draft = false
weight = 2001
+++

## Graph Coding {#graph_coding_a}


### Rewrite paper from the perspective of source coding and planning {#graph_coding_a1}

-   Motivate each solution as a special cost structure of a more general definition
-   Look into source coding of interesting sources, for example, [AoI source coding.](http://dx.doi.org/10.1109/TIT.2020.2983151)
    Then highlight the differences.


#### Goal oriented compression (source coding) {#graph_coding_a1a}

Most of these works are still considering single letter reconstruction.
We are considering reconstruction over multiple time steps, or equivalently, **reconstruction cost minimization**.
This is more general since the distortion/cost is more arbitrary.
The goal-oriented compression section in [Deniz paper](http://dx.doi.org/10.1109/JSAC.2022.3223408) seems particularly relevant. It is related to **remote source coding problem**.
Can we construct a cost structure where acknowledgement is **required** so that we can formulate the whole problem as a cost minimizing source coding?


#### Classical planning problems {#graph_coding_a1b}

The objective in classical path planning problems is to find a plan: a sequence of actions mapping from an initial state to a goal state, in a state space.
The search can be constrained, such as specifying that the plan must pass through certain states or optimized such that the plan contains the fewest actions.
The challenge is due to the combinatorial nature of such problems.
Typically heuristics are used to reduce the search space.
This is similar to **semantics** as heuristics can be considered the semantics to be exploited to make communication easier.
In our case, the heuristics are paths and we want to communicate only the path.


### Optimising the communication tree {#graph_coding_a2}

The solutions we have constructed so far for the _path coding problem_ are based on heuristics and suboptimal.
Even the _dynamic shortest path coding_ is suboptimal because it depends on the optimality of the structure of the tree.
Specifically, the challenge lies in finding a tree structure such that the average cost to reach the goal state is lowest.
On the surface, this seems to be a combinatorial search problem, but a few ideas may help reduce the complexity of the search.


#### Optimal binary search trees {#graph_coding_a2a}

The paper "[optimum binary search trees](https://link.springer.com/content/pdf/10.1007/BF00264289.pdf)" describes a similar problem to the one we have.
The problem therein is to construct a binary search tree where the nodes do not have the same probability.
They describe an algorithm which can run in \\( O(n^2) \\) time.

-   Is it possible to turn this problem into ours?

In the paper, it is said that finding the optimum search tree is equivalent to the problem of "_finding a binary tree of minimum weighted path legnth, with the weights applied from left to right in the tree_".

-   Is this is also true for a m-ary tree?

If so, this might translate to our problem, with \\( m = |\mathcal{C}\_t| \\) (the rate of communication).
