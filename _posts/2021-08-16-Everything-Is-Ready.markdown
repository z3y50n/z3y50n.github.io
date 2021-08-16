---
layout: post
title:  "Everything Is Ready!"
date:   2021-08-16 10:53:25 +0300
categories: GSoC Journal
---

It has been a great experience so far and the last 3 weeks I managed to cross out every remaining item on my checklist for the summer project.

Specifically, I was concerned with the resolution parameter and the support for Directed Graphs.

## Resolution Parameter

With some help from [this](https://doi.org/10.1038/s41598-019-41695-z) paper, this great [quora post](https://www.quora.com/How-is-the-formula-for-Louvain-modularity-change-derived) and some algebra of my own I managed to calculate a valid formula for the modularity gain that includes a resolution parameter. In short, the resolution parameter is a number that affects the size of the communities. The final formula when moving a node $$i$$ to a community $$C$$ is the following:

$$\Delta Q = \frac{k_{i,in}}{2m} - \gamma\frac{ \Sigma_{tot} \cdot k_i}{2m^2}$$

where $$m$$ is the size of the graph, $$k_{i,in}$$ is the sum of the weights of the links from $$i$$ to nodes in $$C$$, $$k_i$$ is the sum of the weights of the links incident to node $$i$$, $$\Sigma_{tot}$$ is the sum of the weights of the links incident to nodes in $$C$$ and $$\gamma$$ is the resolution parameter.

As it is clear the larger the resolution parameter the less will be the modularity gain and thus we will have smaller communities since it would be harder to make a node's move (remember in order to make a move the modularity gain needs to be positive) and the opposite is true when the resolution is getting smaller.

## Directed Graphs

Finally with some minor tweaks presented in [this](https://hal.archives-ouvertes.fr/hal-01231784/document) paper I was able to easily add support for Directed Graphs. The good part is that since the algorithm already supported MultiGraphs, it is now automatically supporting MultiDiGraphs (a class in NetworkX for Multi Directed Graphs). Of course I tested that everything works fine :).
