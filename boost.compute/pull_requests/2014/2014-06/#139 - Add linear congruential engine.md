# #139 Add linear congruential engine [Merged]

> Username: roshanrags  
> Created at: 2014-06-13 11:53:05 UTC  
> Updated at: 2014-06-20 23:33:56 UTC  
> Merged at: 2014-06-18 02:16:26 UTC  
> Closed at: 2014-06-18 02:16:26 UTC  
> Url: https://github.com/boostorg/compute/pull/139  

Added quick and dirty linear congruential engine...

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-06-13 12:20:43 UTC  
> Url: https://github.com/boostorg/compute/pull/139#issuecomment-46004510  

[![Coverage Status](https://coveralls.io/builds/864357/badge)](https://coveralls.io/builds/864357)  
  
Coverage decreased (-0.01%) when pulling **3016af35bb9caff30c334a4aa1e9e756cbefed38 on roshanr95:lce** into **c1d867ba48b85ebba8c7a855e022dd52ebea2b00 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-06-17 01:42:01 UTC  
> Updated_at: 2014-06-17 21:14:48 UTC  
> Url: https://github.com/boostorg/compute/pull/139#discussion_r13839561  

I don't think `lce` is descriptive enough to be a global identifier. I'd prefer removing it and updating the tests/examples to just use `boost::compute::linear_congruential_engine<>`.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-06-17 01:43:51 UTC  
> Url: https://github.com/boostorg/compute/pull/139#issuecomment-46258757  

Left one minor comment. After that it should be good to merge (though it looks like it may need to be rebased on `develop` since I merged your other pull-requests).

---

## Comment 4

> Username: roshanrags  
> Created_at: 2014-06-17 21:46:05 UTC  
> Url: https://github.com/boostorg/compute/pull/139#issuecomment-46369912  

Done...!

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-06-18 02:16:35 UTC  
> Url: https://github.com/boostorg/compute/pull/139#issuecomment-46389193  

Looks good! Merged!

---
