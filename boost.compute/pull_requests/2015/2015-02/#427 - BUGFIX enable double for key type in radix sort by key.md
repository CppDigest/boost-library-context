# #427 BUGFIX enable double for key type in radix sort by key [Merged]

> Username: shehzan10  
> Created at: 2015-02-23 17:17:42 UTC  
> Updated at: 2015-02-23 18:16:07 UTC  
> Merged at: 2015-02-23 18:08:13 UTC  
> Closed at: 2015-02-23 18:08:13 UTC  
> Url: https://github.com/boostorg/compute/pull/427  

- pragma khr_fp64 was not being enabled for key type double

---

## Comment 1

> Username: coveralls  
> Created_at: 2015-02-23 17:41:26 UTC  
> Url: https://github.com/boostorg/compute/pull/427#issuecomment-75592015  

[![Coverage Status](https://coveralls.io/builds/1979662/badge)](https://coveralls.io/builds/1979662)  
  
Coverage increased (+0.0%) to 94.96% when pulling **4961a6177f2e5ccf0e0eb3154b2d6c17fb465a02 on shehzan10:develop** into **de76e66dd1c363842a0ab558578adf21332bac39 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-02-23 18:08:54 UTC  
> Url: https://github.com/boostorg/compute/pull/427#issuecomment-75597488  

Looks great! Thanks!  
  
If possible, could you add a test-case for this?

---

## Comment 3

> Username: shehzan10  
> Created_at: 2015-02-23 18:16:07 UTC  
> Url: https://github.com/boostorg/compute/pull/427#issuecomment-75598890  

While debugging we found that the crashes were only for buffer sizes greater than 32 and only on NVIDIA devices. AMD and Intel devices were running just fine without the pragma.  
  
I'll try adding a test soon.

---
