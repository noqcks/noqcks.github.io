---
layout: post
comments: true
title: Kubernetes Metrics - Understanding Millicores
category: note
---

Kubernetes has a new metrics called Millicores that is used to measure CPU usage.
It is a CPU core split into 1000 units. It's a pretty self evident unit of measure if
you stop to think about it (milli = 1000).

If you have 4 cores, then the CPU capacity of the node is 4000m.

```
4 * 1000m = 4000m
```

If you're using 1/10 of a single core, then you are using 100m.

This was created as a more granular way to determine the usage of cores in a
system where many applications might be using one core; it's easier for humans
to deal with whole numbers than fractions.
