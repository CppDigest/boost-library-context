# #122 Add binary_find algorithm [Closed]

> Username: roshanrags  
> Created at: 2014-05-25 18:03:55 UTC  
> Updated at: 2014-06-04 23:10:44 UTC  
> Closed at: 2014-05-25 18:04:58 UTC  
> Url: https://github.com/boostorg/compute/pull/122  

Added `binary_find` algorithm and changed `upper_bound`, `lower_bound`, `binary_search` to use it as its core. This will also be useful in `partition_point` as well.  
  
This also calls for a small change in my proposal. During the week I have allocated to improve `binary_search`, I will instead improve `binary_find` to take advantage of the partitioned input(Currently it simply uses `find_if`)

---

## Comment 1

> Username: roshanrags  
> Created_at: 2014-05-25 18:04:58 UTC  
> Url: https://github.com/boostorg/compute/pull/122#issuecomment-44141354  

Uh oh , put it against master by mistake.. Will open a new one against develop..

---

## Comment 2

> Username: coveralls  
> Created_at: 2014-05-25 18:33:34 UTC  
> Url: https://github.com/boostorg/compute/pull/122#issuecomment-44141982  

[![Coverage Status](https://coveralls.io/builds/803245/badge)](https://coveralls.io/builds/803245)  
  
Coverage increased (+0.0%) when pulling **1bf09a5624fb62dc9ccad072a6f8032ea862662c on roshanr95:binary_find** into **52886775f8b9ac52c8a44928e60df4c13481c6fd on kylelutz:master**.

---
