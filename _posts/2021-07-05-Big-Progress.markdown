---
layout: post
title:  "Big Progress!"
date:   2021-07-05 10:53:25 +0300
categories: GSoC Journal
---

# Louvain Community Detection
It has been a busy month with trials and errors, but I have implement the algorithm and created the [Pull Request](https://github.com/networkx/networkx/pull/4929) that integrates the Louvain Community Detection inside [NetworkX](https://github.com/networkx/)!

The main body of the algorithm is functional and produces good quality results, similar to other implementations. Next I had to worry about the performance, the API interface and of course to write some tests.

* As for the interface, me and my mentors agreed that using a generator to loop over the dendrogram (remember that the Louvain Algorithm does multiple passes and with each pass creates a partition of the graph which is one level of the dendrogram. At each level the communities get bigger and smaller in number and at the last level one can find the best partition. Read [here](https://en.wikipedia.org/wiki/Louvain_method) for more info) and also write a function that only returns the last level which is the best partition.

* The testing part was also very important, both because I needed to validate that my algorithm actually works and to save myself a lot of time of manually testing and plotting communities every time I made one change to the code.

* Now comes the performance. I have always been hearing that C++ is much faster than Python and I got to experiance that by first hand. To test my performance I run my algorithm with [the Arxiv citation graph dataset](https://www.cs.cornell.edu/projects/kddcup/datasets.html) graph containing 27770 nodes and 352324 edges as input. The C++ implementation of the authors of the algorithm completes the partition in less than a second while my implementation along with [this one](https://github.com/taynaud/python-louvain) take about 12-15 seconds to complete. This is mainly because of the many `for` loops especially on the first pass. So right now I am trying to speed things up as much as possible.

While working on my project I also merged one minor [Pull Request](https://github.com/networkx/networkx/pull/4939) which is always nice. Hopefully I can contribute even more if needed in the future!
