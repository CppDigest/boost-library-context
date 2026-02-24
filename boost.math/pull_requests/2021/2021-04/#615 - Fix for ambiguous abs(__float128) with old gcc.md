# #615 Fix for ambiguous abs(__float128) with old gcc [Merged]

> Username: mborland  
> Created at: 2021-04-26 18:08:31 UTC  
> Updated at: 2021-05-02 12:11:02 UTC  
> Merged at: 2021-04-26 20:11:51 UTC  
> Closed at: 2021-04-26 20:11:51 UTC  
> Url: https://github.com/boostorg/math/pull/615  

An example is [here](https://drone.cpp.al/boostorg/math/180/86/2).

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-04-26 18:59:39 UTC  
> Url: https://github.com/boostorg/math/pull/615#issuecomment-827072329  

Thanks Matt, weird this didn't show up before?

---

## Comment 2

> Username: mborland  
> Created_at: 2021-04-26 19:07:18 UTC  
> Url: https://github.com/boostorg/math/pull/615#issuecomment-827076941  

The failure only shows up on Drone which does not run on PRs from forks. Is it possible to enable those runs?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-04-27 11:35:34 UTC  
> Url: https://github.com/boostorg/math/pull/615#issuecomment-827537969  

>The failure only shows up on Drone which does not run on PRs from forks. Is it possible to enable those runs?  
  
That's a mistake, hopefully fixed now.

---
