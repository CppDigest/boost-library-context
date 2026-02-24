# #575 Remove boost.concept_check dependency [Merged]

> Username: mborland  
> Created at: 2021-03-20 12:05:14 UTC  
> Updated at: 2021-03-22 18:07:20 UTC  
> Merged at: 2021-03-22 17:27:20 UTC  
> Closed at: 2021-03-22 17:27:21 UTC  
> Url: https://github.com/boostorg/math/pull/575  



---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-20 16:54:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/575#pullrequestreview-616927161  

📁 test/compile_test/distribution_concept_check.cpp

```diff
  52 |-    function_requires<DistributionConcept<uniform_distribution<RealType, custom_policy> > >();
  53 |-    function_requires<DistributionConcept<weibull_distribution<RealType, custom_policy> > >();
  22 |+    DistributionConcept<bernoulli_distribution<RealType, custom_policy>> bernoulli;
```

> Username: jzmaddock  
> Created_at: 2021-03-20 16:54:00 UTC  
> Updated_at: 2021-03-20 19:06:40 UTC  
> Url: https://github.com/boostorg/math/pull/575#discussion_r598133818  

I'm not sure, but I don't think this change actually instantiates the concept check at all?

> Username: mborland  
> Created_at: 2021-03-20 17:45:51 UTC  
> Updated_at: 2021-03-20 19:06:40 UTC  
> Url: https://github.com/boostorg/math/pull/575#discussion_r598139494  

It looks like I need to actually call `constraints` with this change.

> Username: mborland  
> Created_at: 2021-03-20 18:16:07 UTC  
> Updated_at: 2021-03-20 19:06:40 UTC  
> Url: https://github.com/boostorg/math/pull/575#discussion_r598142711  

The only files that include `boost/math/concepts/distributions.hpp` are the concept check tests. Rather than re-inventing the wheel on this one how do you feel about just wrapping this header to be disabled if `BOOST_MATH_STANDALONE` is defined?

> Username: jzmaddock  
> Created_at: 2021-03-20 18:17:50 UTC  
> Updated_at: 2021-03-20 19:06:40 UTC  
> Url: https://github.com/boostorg/math/pull/575#discussion_r598142922  

Personally I think it's fine to have dependencies to other boost libraries in our _tests_: it's not at all unreasonable to have to install other dependencies in order to run the tests IMO.


---

## Comment 2

> Username: mborland  
> Created_at: 2021-03-22 16:12:39 UTC  
> Url: https://github.com/boostorg/math/pull/575#issuecomment-804195150  

This one should be good to go.

---
