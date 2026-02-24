# #113 Features/tensor redesign [Closed]

> Username: amitsingh19975  
> Created at: 2021-04-29 09:56:06 UTC  
> Updated at: 2021-05-24 18:52:24 UTC  
> Closed at: 2021-05-24 18:52:23 UTC  
> Url: https://github.com/boostorg/ublas/pull/113  

Fixed issue #111, where the fixed rank extents were producing the wrong shape tensor. This bug arises due to the wrong type of the trait `extent_of_rank_one_array_v` was declared, where instead of `std::size_t`, the type declared was bool. Therefore, whenever the size `N` was inferred, the size cast into a bool, either 0 or 1.

---

## Comment 1

> Username: github-actions[bot]  
> Created_at: 2021-04-29 10:46:41 UTC  
> Url: https://github.com/boostorg/ublas/pull/113#issuecomment-829128074  

This Pull request Passed all of clang-tidy tests. :+1:

---
