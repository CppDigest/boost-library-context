# #228 Merge algorithm [Merged]

> Username: roshanrags  
> Created at: 2014-08-10 02:14:27 UTC  
> Updated at: 2014-08-12 04:44:37 UTC  
> Merged at: 2014-08-12 02:59:18 UTC  
> Closed at: 2014-08-12 02:59:18 UTC  
> Url: https://github.com/boostorg/compute/pull/228  

Added a merge_path kernel and a merge algorithm based on it. Also changed merge to use the new algorithm.  
  
For now, I have changed only the overload without the comparison function. I'm planning to work on adding overloads for many functions to more closely match their STL counterparts after my GSoC and I'll change this one then.

---

## Comment 1

> Username: roshanrags  
> Created_at: 2014-08-10 02:24:03 UTC  
> Url: https://github.com/boostorg/compute/pull/228#issuecomment-51704163  

I'm not exactly able to measure the performance gain since the previous version gives me `Invalid command queue` errors but the new version is ~40x faster than its STL counterpart.

---

## Comment 2

> Username: coveralls  
> Created_at: 2014-08-10 02:28:02 UTC  
> Url: https://github.com/boostorg/compute/pull/228#issuecomment-51704221  

[![Coverage Status](https://coveralls.io/builds/1064520/badge)](https://coveralls.io/builds/1064520)  
  
Coverage decreased (-0.02%) when pulling **c48760fd90205329ff49cb06c8517a8a255defcd on roshanr95:merge** into **bd427b8a1bdb8564f0438d44bbdbb40bf3fce4f6 on kylelutz:develop**.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-08-12 02:59:25 UTC  
> Url: https://github.com/boostorg/compute/pull/228#issuecomment-51868026  

Looks great! Merged!

---
