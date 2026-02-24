# #597 Fix find_end algorithm [Merged]

> Username: jszuppe  
> Created at: 2016-04-24 20:15:16 UTC  
> Updated at: 2016-04-27 17:46:19 UTC  
> Merged at: 2016-04-26 04:37:46 UTC  
> Closed at: 2016-04-26 04:37:46 UTC  
> Url: https://github.com/boostorg/compute/pull/597  

I've found out what's wrong in the `find_end` algorithm. Using uninitialized memory (from the end of `matching_indices`) was causing undefined behaviour. It was uninitialized because `search_kernel` does not initialize the last `<pattern_size> - 1` elements of `matching_indices` as the pattern can not start there. Now it's fixed.

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-04-25 00:41:35 UTC  
> Url: https://github.com/boostorg/compute/pull/597#issuecomment-214076544  

[![Coverage Status](https://coveralls.io/builds/5912955/badge)](https://coveralls.io/builds/5912955)  
  
Coverage increased (+0.005%) to 88.796% when pulling **701d6078d84656d121c8e1dcc069d5b3fb847b8f on haahh:pr_fix_find_end** into **48217d2918d64ac84c13822384c67c4edcad5481 on boostorg:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-04-26 04:37:53 UTC  
> Url: https://github.com/boostorg/compute/pull/597#issuecomment-214613927  

Thanks for fixing this!

---
