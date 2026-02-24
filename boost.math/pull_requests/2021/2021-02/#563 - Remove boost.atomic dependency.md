# #563 Remove boost.atomic dependency [Merged]

> Username: mborland  
> Created at: 2021-02-27 18:50:28 UTC  
> Updated at: 2021-03-10 19:20:07 UTC  
> Merged at: 2021-03-10 19:15:17 UTC  
> Closed at: 2021-03-10 19:15:17 UTC  
> Url: https://github.com/boostorg/math/pull/563  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-02-27 18:53:04 UTC  
> Url: https://github.com/boostorg/math/pull/563#issuecomment-787118634  

Thanks Matt, looks good.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-09 18:04:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/563#pullrequestreview-607708332  

📁 include/boost/math/concepts/real_concept.hpp

```diff
  63 |-    real_concept() : m_value(0){}
  64 |-    real_concept(char c) : m_value(c){}
  63 |+    real_concept() noexcept : m_value(0){}
```

> Username: jzmaddock  
> Created_at: 2021-03-09 18:04:08 UTC  
> Updated_at: 2021-03-10 15:42:22 UTC  
> Url: https://github.com/boostorg/math/pull/563#discussion_r590599793  

Just curious: does this fix an actual issue?  
  
I ask because this type is meant to model the minimum conceptual requirements for a floating point type, and in general, we cannot guarantee a `noexcept` constructor - think multiprecision::mpfr_float for example.

> Username: mborland  
> Created_at: 2021-03-09 18:19:11 UTC  
> Updated_at: 2021-03-10 15:42:22 UTC  
> Url: https://github.com/boostorg/math/pull/563#discussion_r590609949  

I believe that it will fix the CI failures. `std::atomic` requires a `noexcept` constructor so attempting to use `Real = real_concept` yields:  
  
```  
/usr/include/c++/9/atomic:198:7: note: ‘std::atomic<_Tp>::atomic() noexcept [with _Tp = boost::math::concepts::std_real_concept]’ is implicitly deleted because its exception-specification does not match the implicit exception-specification ‘’  
```  
  
If this throws off your conceptual requirements I think something to the effect of `std::enable_if_t<std::is_nothrow_constructible<Real>, bool> = true` would work. Either way I am now seeing that this has the potential to break user code.

> Username: NAThompson  
> Created_at: 2021-03-09 18:20:29 UTC  
> Updated_at: 2021-03-10 15:42:22 UTC  
> Url: https://github.com/boostorg/math/pull/563#discussion_r590610830  

Isn't there a chance that user code was already broken?

> Username: jzmaddock  
> Created_at: 2021-03-09 18:34:24 UTC  
> Updated_at: 2021-03-10 15:42:22 UTC  
> Url: https://github.com/boostorg/math/pull/563#discussion_r590620538  

>Isn't there a chance that user code was already broken?  
  
Yes.  
  
boost::atomic requires a trivially constructible type, and real_concept does not meet that requirement.  Nor should it IMO.  
  
So.... either we document the requirements on the Monte Carlo code that the template type must be trivially constructible and then either:  
  
1) don't test with real_concept: the code isn't usable with generic FP types, or  
2) Provide a new concept, lets say trivial_real_concept that models this requirement and test with that.  
  
Or   
  
3) just through some serious hoops to allow the code to be used with arbitrary FP types.  
  
I tend to lean towards (1).

> Username: jzmaddock  
> Created_at: 2021-03-09 18:35:16 UTC  
> Updated_at: 2021-03-10 15:42:22 UTC  
> Url: https://github.com/boostorg/math/pull/563#discussion_r590621163  

Sorry, just to say, real_concept shouldn't change, at least not in this way - it doesn't model the required concept any more.

> Username: NAThompson  
> Created_at: 2021-03-10 13:01:02 UTC  
> Updated_at: 2021-03-10 15:42:22 UTC  
> Url: https://github.com/boostorg/math/pull/563#discussion_r591477234  

Yeah, recovering even double precision with a Monte-Carlo integration already takes roughly the age of the universe, so I don't see any particularly compelling reason to make sure it works with multiprecision.

> Username: mborland  
> Created_at: 2021-03-10 15:45:03 UTC  
> Url: https://github.com/boostorg/math/pull/563#discussion_r591635185  

I went with option 1 and reduced the allowable types to those that are trivially copyable (the concept requirement for `std::atomic`). This is reflected in the docs as well.


---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2021-03-10 18:18:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/563#pullrequestreview-609025625  

📁 doc/quadrature/naive_monte_carlo.qbk

```diff
  14 | 
  15 |-     template<class Real, class F, class RNG = std::mt19937_64, class Policy = boost::math::policies::policy<>>
  15 |+     template<class Real, class F, class RNG = std::mt19937_64, class Policy = boost::math::policies::policy<>,
```

> Username: NAThompson  
> Created_at: 2021-03-10 18:18:01 UTC  
> Url: https://github.com/boostorg/math/pull/563#discussion_r591760063  

We really should get PCG into boost.random. Mersenne twister is just an awful RNG>

> Username: mborland  
> Created_at: 2021-03-10 18:30:18 UTC  
> Url: https://github.com/boostorg/math/pull/563#discussion_r591769010  

It seems difficult to get even trivial changes into boost.random...


---

## Comment 4

> Username: mborland  
> Created_at: 2021-03-10 18:51:59 UTC  
> Url: https://github.com/boostorg/math/pull/563#issuecomment-795905344  

This should be good to go. CI was clean on last run with the real_concept test being removed.

---
