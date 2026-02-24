# #123 Add binary_find algorithm [Closed]

> Username: roshanrags  
> Created at: 2014-05-25 18:05:49 UTC  
> Updated at: 2014-06-04 23:10:44 UTC  
> Closed at: 2014-05-29 16:37:49 UTC  
> Url: https://github.com/boostorg/compute/pull/123  

Added `binary_find` algorithm and changed `upper_bound`, `lower_bound`, `binary_search` to use it as its core. This will also be useful in `partition_point` as well.  
  
This also calls for a small change in my proposal. During the week I have allocated to improve `binary_search`, I will instead improve `binary_find` to take advantage of the partitioned input(Currently it simply uses `find_if`)

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-05-25 18:29:25 UTC  
> Url: https://github.com/boostorg/compute/pull/123#issuecomment-44141908  

[![Coverage Status](https://coveralls.io/builds/803251/badge)](https://coveralls.io/builds/803251)  
  
Coverage increased (+0.0%) when pulling **1bf09a5624fb62dc9ccad072a6f8032ea862662c on roshanr95:binary_find** into **8cfaf793f6e9eda2eb1c78ebf8f6dc98ccc33f52 on kylelutz:develop**.

---

## Comment 2

> Username: roshanrags  
> Created_at: 2014-05-29 16:37:49 UTC  
> Url: https://github.com/boostorg/compute/pull/123#issuecomment-44553290  

Included in #125.

---
