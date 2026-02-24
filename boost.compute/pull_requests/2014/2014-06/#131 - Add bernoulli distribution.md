# #131 Add bernoulli distribution [Merged]

> Username: roshanrags  
> Created at: 2014-06-04 23:09:50 UTC  
> Updated at: 2014-06-20 23:34:01 UTC  
> Merged at: 2014-06-05 01:41:37 UTC  
> Closed at: 2014-06-05 01:41:37 UTC  
> Url: https://github.com/boostorg/compute/pull/131  

I added `bernoulli_distribution`. And one small thing, any reason why `normal_distribution` isn't in the `random.hpp` header? I'll add it in my next pull if it should be there...

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-06-04 23:51:14 UTC  
> Url: https://github.com/boostorg/compute/pull/131#issuecomment-45166618  

[![Coverage Status](https://coveralls.io/builds/835994/badge)](https://coveralls.io/builds/835994)  
  
Coverage increased (+0.02%) when pulling **1777b7e5bf4fd3e766ce97b43669a9eae1ecba30 on roshanr95:bernoulli-distribution** into **cfc3a7ebf1bacc8fef0b657b62885fbd5f305aa2 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-06-05 01:41:39 UTC  
> Url: https://github.com/boostorg/compute/pull/131#issuecomment-45172580  

Looks good. Merged!

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-06-05 01:42:07 UTC  
> Url: https://github.com/boostorg/compute/pull/131#issuecomment-45172607  

And yeah, the `normal_distribution.hpp` header should be included in `random.hpp`, seems I just forgot to add it.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-06-05 02:10:38 UTC  
> Url: https://github.com/boostorg/compute/pull/131#issuecomment-45174012  

Fixed the `random.hpp` header, commit is here: 53c3e1af830590cb29a228f64abab4b14e0c166c.

---
