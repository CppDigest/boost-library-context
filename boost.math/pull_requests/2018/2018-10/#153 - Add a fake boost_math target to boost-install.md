# #153 Add a fake boost_math target to boost-install [Merged]

> Username: pdimov  
> Created at: 2018-10-05 17:48:47 UTC  
> Updated at: 2019-10-24 22:10:27 UTC  
> Merged at: 2018-10-08 16:55:22 UTC  
> Closed at: 2018-10-08 16:55:22 UTC  
> Url: https://github.com/boostorg/math/pull/153  

This adds a target `boost_math` that does nothing to the `boost-install` line for dependency resolution purposes. Some libraries such as `random` depend on `math`, but there's no `math`, so the fake target helps with that.

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-10-07 22:42:58 UTC  
> Url: https://github.com/boostorg/math/pull/153#issuecomment-427693504  

Comments, reservations?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-10-08 16:55:17 UTC  
> Url: https://github.com/boostorg/math/pull/153#issuecomment-427906392  

Just not got to it yet :(  
  
Merging now.

---
