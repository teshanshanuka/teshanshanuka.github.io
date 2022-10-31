---
layout: post
title:  "Profiling Python Code"
date:   2022-10-31 01:01:01 +0530
categories: python performance profiling
---

*Copy pasta for when I need to profile python code again*

---

# cProfile

> Built in so no `pip install` needed. 

```python
import cProfile

pr = cProfile.Profile()
pr.enable()

# *** Code Here *** #

pr.disable()
pr.print_stats()
```

## View stats with snakeviz

[Snakeviz][1] is a nice browser based graphical viewer for `cProfile` 

```python
pr.dump_stats('filename.prof')
```

```sh
pip install snakeviz
snakeviz filename.prof
```

[Here][2] there are more ways to analyze the `cProfille` dumps.

# pprofile

Gives out a nice output that has a line by line profiling info for the code. [PyPi][3]

```python
# Example from the PyPi page https://pypi.org/project/pprofile/
import pprofile

def someHotSpotCallable():
    # Deterministic profiler
    prof = pprofile.Profile()
    with prof():
        # *** Code to profile *** #
    prof.print_stats()

def someOtherHotSpotCallable():
    # Statistic profiler
    prof = pprofile.StatisticalProfile()
    with prof(
        period=0.001, # Sample every 1ms
        single=True, # Only sample current thread
    ):
        # *** Code to profile *** #
    prof.print_stats()
```

[1]: https://pypi.org/project/snakeviz/
[2]: https://stackoverflow.com/questions/29630667/how-can-i-analyze-a-file-created-with-pstats-dump-statsfilename-off-line
[3]: https://pypi.org/project/pprofile/
