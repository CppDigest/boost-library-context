# #593 Fix radix_sort_by_key for desc order [Merged]

> Username: jszuppe  
> Created at: 2016-04-23 16:52:32 UTC  
> Updated at: 2016-04-24 20:29:16 UTC  
> Merged at: 2016-04-24 17:56:56 UTC  
> Closed at: 2016-04-24 17:56:56 UTC  
> Url: https://github.com/boostorg/compute/pull/593  

This should fix https://github.com/boostorg/compute/issues/588.  
  
Now in radix sort when `greater<T>()` is specified as comparison function (desc order) instead of performing asc-ordered radix sort and then reversing the result, descending radix sort is performed.  
  
It's done by negating input value if its type is a signed type and by subtracting the input value from the maximum value of value's type in other cases (in `radix()` function). That way values seem "reversed" and we get desc order. It should also be faster than sorting in asc order and then reversing the result.  
  
I also added some test for desc-ordered radix sort and radix sort by key.

---

## Comment 1

> Username: pavanky  
> Created_at: 2016-04-23 17:34:10 UTC  
> Url: https://github.com/boostorg/compute/pull/593#issuecomment-213792151  

looks good :+1:

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-04-23 17:55:29 UTC  
> Url: https://github.com/boostorg/compute/pull/593#issuecomment-213796717  

[![Coverage Status](https://coveralls.io/builds/5905081/badge)](https://coveralls.io/builds/5905081)  
  
Coverage increased (+0.1%) to 88.791% when pulling **f29bbda7f89ee7cc7bb89309a7a4f7f5dcc7d592 on haahh:pr_sort_by_key_desc_fix** into **437363b6b70784193085a0af1dd55f9d435e39de on boostorg:develop**.

---
