# #345 BUG FIX: sort_by_key now works with larger vector sizes. [Merged]

> Username: pavanky  
> Created at: 2014-12-15 20:13:12 UTC  
> Updated at: 2014-12-16 07:20:11 UTC  
> Merged at: 2014-12-16 04:13:44 UTC  
> Closed at: 2014-12-16 04:13:44 UTC  
> Url: https://github.com/boostorg/compute/pull/345  

- sort_by_key now calls radix_sort for size >= 32 even with Compare  
- Fixes #343

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-12-15 20:35:33 UTC  
> Url: https://github.com/boostorg/compute/pull/345#issuecomment-67060780  

[![Coverage Status](https://coveralls.io/builds/1614913/badge)](https://coveralls.io/builds/1614913)  
  
Coverage decreased (-0.01%) when pulling **10890fd7d1065b1efd3062d48ed3482f01e6d155 on pavanky:sort_fix** into **c6f38bc56743ea6b2356082d03232f683d785958 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-12-16 04:13:51 UTC  
> Url: https://github.com/boostorg/compute/pull/345#issuecomment-67110283  

Awesome! Thanks!

---
