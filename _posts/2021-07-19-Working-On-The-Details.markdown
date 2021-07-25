---
layout: post
title:  "Working On The Details"
date:   2021-07-19 10:53:25 +0300
categories: GSoC Journal
---

On the last update I was busy improving the performance of the algorithm. With some tips and tricks, some suggested by my mentors [Dan](https://github.com/dschult) and [Mridul](https://github.com/MridulS), I managed to reduce the timing by about 2-3 seconds.

While the performance doesn't compare with the C++ original implementation, it still is quite acceptable at this stage and we decided to focus on some other issues and details missing.

Mainly I needed to correctly handle the `weight` attribute being passed as input to the algorithm. Especially when it was `None` the algorithm wasn't working. As a way around that at the beginning of the code instead of just copying the given graph, I create a new one with only one attribute as the weight. After that all the functions will be using the same `"weight"` name as their attribute that holds the edge's weight. Of course I added a test to check whether passing None value works properly!

I also added more extensive documentation which is always nice and important to have and finally I added suppport for MultiGraphs by correctly converting them to normal Graphs at the beginning.

Things are looking good so far. Next thing on my checklist is to add the resolution parameter, which isn't very simple because there are multiple definitions of resolution in modularity and I have to do some research as to which one to use. After that I guess the code should be ready for final review and possibly to be merged.

There is also one optional task remaining to make the Louvain Community Detection Algorithm to work with Directed Graphs. If time permits I would definitely try to add this support during the GSoC (hopefully it doesn't require much changes in the code or possibly another PR), but in either way I will implement the Directed version even after the project!

Till next time!
