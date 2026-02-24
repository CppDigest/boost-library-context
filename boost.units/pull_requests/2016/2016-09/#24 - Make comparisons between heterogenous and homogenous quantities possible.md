# #24 Make comparisons between heterogenous and homogenous quantities possible  [Open]

> Username: muggenhor  
> Created at: 2016-09-08 14:33:06 UTC  
> Updated at: 2021-06-10 02:34:20 UTC  
> Url: https://github.com/boostorg/units/pull/24  

Make comparisons between heterogenous and homogenous quantities possible .  
  
And add a test case that fails without this change.  
  
NOTE that the explicit conversion to `quantity<Unit1, Y>` is probably not necessary. It definitely isn't necessary if conversion_factor(Unit2(), Unit1()) == 1, and I believe that it is. Unfortunately I haven't been able to verify that through reviewing the code involved in computing the conversion factor.

---

## Comment 1

> Username: muggenhor  
> Created_at: 2017-07-13 10:53:58 UTC  
> Updated_at: 2018-02-23 10:23:53 UTC  
> Url: https://github.com/boostorg/units/pull/24#issuecomment-315044449  

@swatanabe what's your opinion on this?

---
