# #772 Fix out of bound access in local_scan kernel [Merged]

> Username: umar456  
> Created at: 2018-04-10 06:42:16 UTC  
> Updated at: 2019-02-10 10:51:43 UTC  
> Merged at: 2019-02-10 10:51:43 UTC  
> Closed at: 2019-02-10 10:51:43 UTC  
> Url: https://github.com/boostorg/compute/pull/772  

The `local_scan` kernel was accessing the first buffer using the global id without checking if it was within the bounds.

---

## Comment 1

> Username: coveralls  
> Created_at: 2018-04-10 07:52:16 UTC  
> Url: https://github.com/boostorg/compute/pull/772#issuecomment-380008220  

[![Coverage Status](https://coveralls.io/builds/16431954/badge)](https://coveralls.io/builds/16431954)  
  
Coverage remained the same at 82.121% when pulling **86b36bae6ed6aaeb23c295d0910f886236e91742 on umar456:scan_fix** into **1bf4b53f6051fb8f20823a0c780cfcafeb4175c0 on boostorg:develop**.

---

## Comment 2

> Username: umar456  
> Created_at: 2018-06-01 01:32:42 UTC  
> Url: https://github.com/boostorg/compute/pull/772#issuecomment-393732037  

I found this by running cuda-memcheck. Is there anything else I should do to get this merged?

---
