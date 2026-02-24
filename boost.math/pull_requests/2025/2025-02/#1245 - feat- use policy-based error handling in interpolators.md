# #1245 feat: use policy-based error handling in interpolators [Open]

> Username: fpelliccioni  
> Created at: 2025-02-12 16:49:05 UTC  
> Updated at: 2025-02-13 19:13:43 UTC  
> Url: https://github.com/boostorg/math/pull/1245  

This PR updates `cubic_hermite`, `makima`, and `pchip` interpolators to use `boost::math::policies::raise_domain_error` instead of throwing exceptions.   
This aligns their error handling with Boost.Math’s policy-based approach.  
  
# Related PR  
#1244 depends on this PR.  
  
# Summary  
Replaces exceptions with policy-based error handling.  
Ensures compatibility with compile-time tests where exceptions are disabled.  
  
# Next Steps  
Once merged, #1244 will be unblocked and ready for review.

---

## Comment 1

> Username: mborland  
> Created_at: 2025-02-12 17:07:15 UTC  
> Url: https://github.com/boostorg/math/pull/1245#issuecomment-2654347496  

I'm not a fan of changing the API to add policy support unless you have a compelling reason. I think this could simply have replaced the current `throw std::whatever` to using the boost policy for signaling errors.   
  
I feel like this is un-ergonomic:  
```  
   auto ret = boost::math::interpolators::pchip<std::vector<double>>::create(std::move(data_x), std::move(data_y), 3, 2);  
   check_result<double>(ret.first->operator()(1.0));  
```  
  
compared to:  
```  
boost::math::interpolators::pchip<std::vector<double>> s(std::move(data_x), std::move(data_y), 3, 2);  
   check_result<double>(s(1.0));  
```  
  
CC: @NAThompson

---

## Review 2 [Commented]

> Username: mborland  
> Created_at: 2025-02-13 17:18:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1245#pullrequestreview-2615730969  

I like this a lot more than the first iteration.

📁 include/boost/math/interpolators/detail/cubic_hermite_detail.hpp

```diff
  36 |+             error_msg_ = "There must be the same number of ordinates as abscissas.";
  37 |+             valid_ = false;
  38 |+             return;
```

> Username: mborland  
> Created_at: 2025-02-13 17:15:39 UTC  
> Updated_at: 2025-02-13 17:18:16 UTC  
> Url: https://github.com/boostorg/math/pull/1245#discussion_r1954912648  

So that we don't change the existing behavior what about something like:  
  
```  
boost::math::policies::raise_domain_error(...)  
error_msg_ = ...;  
valid_ = false;  
return;  
```  
  
The raise domain error will throw when exceptions are enabled, and when exceptions are disabled we use track the validity of the state. This is the implementation of raise_domain_error: https://github.com/boostorg/math/blob/develop/include/boost/math/policies/error_handling.hpp#L208-L222. Like the comment says you shouldn't be able to hit the actual return value, but the continued implementations below will write to errno, NOP, etc. depending on the policy.

> Username: jzmaddock  
> Created_at: 2025-02-13 18:07:05 UTC  
> Updated_at: 2025-02-13 18:07:06 UTC  
> Url: https://github.com/boostorg/math/pull/1245#discussion_r1954995496  

+1 on that.


📁 test/test_hypergeometric_dist.cpp

```diff
 503 |+ //       0.1);  // Interpolación lineal
 504 |+ 
 505 |+ //    // Valores fuera del soporte deben dar 0
```

> Username: mborland  
> Created_at: 2025-02-13 17:17:22 UTC  
> Updated_at: 2025-02-13 17:18:16 UTC  
> Url: https://github.com/boostorg/math/pull/1245#discussion_r1954914988  

If you leave this comments in can you please translate them to English.

> Username: mborland  
> Created_at: 2025-02-13 17:19:47 UTC  
> Updated_at: 2025-02-13 17:19:48 UTC  
> Url: https://github.com/boostorg/math/pull/1245#discussion_r1954918575  

I forgot the doc bot doesn't like non-ascii characters too: https://circleci.com/api/v1.1/project/github/boostorg/math/2184/output/108/0?file=true&allocation-id=67ae20f0a47cbf740e551f62-0-build%2FABCDEFGH

> Username: fpelliccioni  
> Created_at: 2025-02-13 18:24:43 UTC  
> Url: https://github.com/boostorg/math/pull/1245#discussion_r1955021272  

Sorry, that was commited by mistake, reverting...


---

## Review 3 [Approved]

> Username: mborland  
> Created_at: 2025-02-13 19:13:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1245#pullrequestreview-2615999074  

LGTM now. I kicked off the CI run to make sure it agrees.

---
