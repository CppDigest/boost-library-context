# #104 Add dual-sequence overloads to transform_inclusive_scan() and transform_exclusive_scan() [Open]

> Username: jgopel  
> Created at: 2022-06-06 00:36:11 UTC  
> Updated at: 2022-06-06 00:36:11 UTC  
> Url: https://github.com/boostorg/algorithm/pull/104  

There are dual-sequence overloads for `transform()` (this overload is called `zip` in may other languages) and `transform_reduce()`, in the standard, but there is no similar version for `transform_inclusive_scan()` or `transform_exclusive_scan()`. This patch-set also adds `constexpr` support to the existing overloads.  
  
h/t to @elbeno for suggesting implementing this.

---
