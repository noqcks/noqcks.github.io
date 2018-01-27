---
title: Save Yourself From The OOM Killer
date: '2016-11-21'
categories:
  - linux
  - kernel
tags:
  - linux
slug: save-yourself-from-the-oom-killer
---


The company I work for began seeing a lot of this coming from instances with
very low memory.

```
error="cannot connect to Docker endpoint"
```

and also noticed that there seemed to be plenty of available memory just sitting
here in the cache... What gives kernel?

```
total       used       free     shared    buffers     cached
Mem:      15669064   15121416     547648        572     948596
8265488
-/+ buffers/cache:    5907332    9761732
Swap:            0          0          0
```

It's ominous, yes, but perfectly explainable. The Docker daemon wasn't serving
requests anymore because it had been executed by the OOM Killer. There's an
excellent blog post on LWN over [here](https://lwn.net/Articles/317814/) that
explains it in depth a lot better that I ever could, but I'll break it down.

Essentially, "out-of-memory (OOM) killer kicks in and picks a process to kill
using a set of heuristics which has evolved over time". The reason this exists
in the first place is because the kernel was designed to over-commit memory to
try make memory usage on your machines as efficient as possible. It has some
downsides (like sometimes your cute little docker daemon gets iced),
but is important to ensure our machines use the very most of their memory
at all times.

These heuristics mentioned in the quote above is just a sorted record of who's
"bad" according to the system. These "baddies" are scored from -16 to +15 at
`/proc/<pid>/oom_score` based on a number of different factors (run time, CPU time,
etc). The OOM Killer selects the most bad and executes them when system memory
gets too low. A score of -16 is least likely to be killed, a score of +15 is most
likely to be killed, and a special number of -17 is entirely exempt.

Now that we know about OOM Killer, we probably can understand how Docker the
resource hog can sometimes be executed. If you really wanna stop Docker from
feeling the OOM, one thing you can do is modify `min_free_kbytes`. This is the
minimum number of free `kb` to keep across the machine (not used in any caches).
In the case that you encounter OOM situations when in fact there is available
memory sitting in the cache, you can set it to a higher level in order to
prevent this. Sometimes the kernal just isn't that good at evicting the cache when
processes ask for memory  ¯ \\_(ツ)_/¯. I've seen recommendations saying that around 5-10% of
physical memory is reasonable. It's a finicky setting, because if you set it too low,
then OOM can't even run and you'll deadlock the machine but if you set it too high you'll OOM
the machine almost instantly.

You can see the default setting for your machine by querying sysctl:

```
↪ sysctl -n vm.min_free_kbytes
10978
```

And you can set it to a new value by doing:

```
sysctl -w vm.min_free_kbytes=20978
```

Or if you prefer just adding a line to `/etc/sysctl.conf` directly.

I really hope this was informative ✌.
