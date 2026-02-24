# #191 vectorstream: support file sizes larger than INT_MAX [Merged]

> Username: grrtrr  
> Created at: 2023-01-09 18:55:47 UTC  
> Updated at: 2024-07-07 13:47:54 UTC  
> Merged at: 2024-07-06 20:44:52 UTC  
> Closed at: 2024-07-06 20:44:52 UTC  
> Url: https://github.com/boostorg/interprocess/pull/191  

File sizes larger than INT_MAX cause segmentation faults, due to the use of `pbump/gbump`, which only support `int` increments.  
  
Rewrite `int`-based offset adjustments to use streamoff offsets instead.  
  
Fixes #190.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2024-07-06 20:44:58 UTC  
> Url: https://github.com/boostorg/interprocess/pull/191#issuecomment-2211962732  

Many thanks!

---
