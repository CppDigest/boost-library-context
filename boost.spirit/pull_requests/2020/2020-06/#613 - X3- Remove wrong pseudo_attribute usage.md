# #613 X3: Remove wrong pseudo_attribute usage [Merged]

> Username: Kojoley  
> Created at: 2020-06-20 21:26:44 UTC  
> Updated at: 2020-06-21 23:38:05 UTC  
> Merged at: 2020-06-21 23:38:03 UTC  
> Closed at: 2020-06-21 23:38:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/613  

We already have an actual attribute here.  
  
This reverts a wrong part of ded2c2127c3680a10284d07d0efb6e8cf24fd90a, that also prevented guaranteed copy elision.

---
