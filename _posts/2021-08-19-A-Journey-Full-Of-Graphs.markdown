---
layout: post
title:  "A Journey Full of Graphs"
date:   2021-08-19 11:23:25 +0300
categories: GSoC Journal
---

# A little introduction
In discrete mathematics, a **graph** is a structure amounting to a set of objects in which some pairs of the objects in which some pairs of them are in some sense "related". The objects are called vertices (also nodes) and each of the related pairs of vertices is called an edge. Fancy definition for just a simple representation that everyone must have seen one. But let's give an example graph.

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/5b/6n-graf.svg/1280px-6n-graf.svg.png">

So as it is very obvious the circles are the so-called nodes and the edges are the lines that connect the dots.

The organization I worked for during the summer (and will keep contributing afterwards) is [NetworkX](https://networkx.org) and is a Python package that implements data structures for graphs and a lot more tools in order to explore this structure.

Now there are a lot of things we can say about graphs (it would take a lot of time and it isn't part of this blog's scope) but I will stick to my project.

# Community Detection
The area of *community detection* focuses on grouping different nodes of a graph based on similar characteristics or the number of edges that connect them.

<img src="https://miro.medium.com/max/738/1*skhjgApyDIPbJNolIZCF5w.png">

As we can see in the above image we can find some inner structure inside our graph, meaning that some nodes are more connected than others. We can basically group them together and say that these nodes belong to the same community. Grouping nodes of a network into communities has a lot of value since it can reveal a lot of information about the network itself. Communities can allow us to create a large scale map of a network since individual communities act like meta-nodes in the network which makes it easier to study. Also, communities often have very different properties than the average properties of the graph, giving us much more important information about the network etc.

So community detection is quite important. But you may wonder, how do we do it? We can't really give a definitive answer to this question, because a community is an abstract idea. Also, the partition of a network into communities of densely connected nodes, with the nodes belonging to different communities being only sparsely connected is very hard computationally. In any case, there are a lot of algorithms that try to group nodes together based on some measures or the number of edges between nodes in reasonable timings.

# Louvain Community Detection Algorithm
One of the algorithms I mentioned above is the [Louvain method](https://arxiv.org/pdf/0803.0476.pdf). It is a [heuristic](https://en.wikipedia.org/wiki/Heuristic) method (meaning that it isn't perfect but produces quite good and sufficient results) to extract the community structure of large networks. The method is based on [modularity](https://en.wikipedia.org/wiki/Modularity_(networks)) optimization and it is shown to be particularly fast and produces very good quality communities based on the modularity measure.

The modularity of a partition is a scalar value between -1 and 1 that measures the density of links inside communities as compared to links between communities. In the case of weighted networks (weighted networks are networks that have weights on their links), it is defined as:

$$Q = \frac{1}{2m}\sum_{i,j}\left[A_{ij} - \frac{k_{i}k_{j}}{2m}\right]\delta(c_{i}, c_{j})$$

where $$A_{ij}$$ represents the weight of the edge between $$i$$ and $$j$$, $$k_i=\sum_jA_{ij}$$ is the sum of the weights of the edges attached to vertex $$i$$, $$c_i$$ is the community to which vertex $$i$$ is assigned, the $$\delta$$-function $$\delta(u, v)$$ is 1 if $$u=v$$ and 0 otherwise and $$m=\frac{1}{2}\sum_{i,j}A_{ij}$$.

# The Louvain Method
Now that we have made some necessary definitions, let's explore how the Louvain Community Detection algorithm works. The method consists of two phases. Assuming a weighted network of $$N$$ nodes, we first assign a different community to each node. Then, for each node $$i$$ we consider the neighbours $$j$$ of $$i$$ and we evaluate the gain of modularity that would take place by removing $$i$$ from its community and by placing it in the community of $$j$$. The node $$i$$ is then placed in the community for which this gain is maximum, but only if the gain is positive. If no positive gain is possible, $$i$$ stays in its original community. We repeat this process for all nodes until no further moves can occur. The latter sentence means that each node may be considered multiple times. The above process signifies the first phase. Let's also note that the order with which we consider nodes do not seem to affect much the final modularity of the partition but can influence the computational time. The first phase is efficient because it is quite easy to compute the modularity gain $$\Delta Q$$ by moving a node $$i$$ into a community $$C$$ from the following formula:

$$\Delta Q = \frac{k_{i,in}}{2m} - \gamma\frac{\Sigma_{tot} \cdot k_i}{2m^2}$$

where $$m$$ is the size of the graph, $$k_{i,in}$$ is the sum of the weights of the links from $$i$$ to nodes in $$C$$, $$k_i$$ is the sum of the weights of the links incident to node $$i$$, $$\Sigma_{tot}$$ is the sum of the weights of the links incident to nodes in $$C$$ and $$\gamma$$ is the resolution parameter (which can give a bias towards bigger communities if is is smaller than $$1$$ and the smaller communities otherwise).


The second phase consists of building a new graph whose nodes are now the communities that we found during the first phase and the weights of the new links between the new nodes are given by the sum of the weight of the links between nodes in the corresponding communities. Links between nodes of the same community lead to self-loops. Once the new graph is created, it is possible to reapply the first phase.

We therefore, define a "pass" to be a combination of these two phases. The passes are iterated until there are no more changes and a local maximum of modularity it achieved. Consequently, the algorithm incorporates a notion of hierarchy, since a partition is created after each pass. An small example illustration of the method can be found in the following image where we see the grouping of nodes (first phase) and the creation of a new graph (second phase) and the second pass where the process repeats producing a smaller network at each step.

<img src="https://www.researchgate.net/profile/Patricia-Conde-Cespedes/publication/263012166/figure/fig1/AS:296088050389004@1447604300798/Louvain-algorithm-overview-Fig-1-in-10.png">

On first thought the algorithm can seem particularly slow given that we iterate all the nodes more than once, but the efficiency comes from the fact that the number of communities decreases drastically after just a few passes and so most of the computation takes place on the first iterations.

Just for completeness before I jump into the code presentation, I provide also the formula for modularity gain of a directed networkx in order to show that it is actual easy to make the algorithm compatible with directed graphs.

$$\Delta Q = \frac{k_{i,in}}{m} - \gamma\frac{k_i^{out} \cdot\Sigma_{tot}^{in} + k_i^{in} \cdot \Sigma_{tot}^{out}}{m^2}$$


# Implementing everything in Python
