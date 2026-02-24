# #588 Remove boost.integer dependency [Closed]

> Username: mborland  
> Created at: 2021-03-29 16:59:06 UTC  
> Updated at: 2021-04-02 11:53:04 UTC  
> Closed at: 2021-04-02 11:53:04 UTC  
> Url: https://github.com/boostorg/math/pull/588  



---

## Comment 1

> Username: mborland  
> Created_at: 2021-03-30 17:20:27 UTC  
> Url: https://github.com/boostorg/math/pull/588#issuecomment-810437617  

@jzmaddock This is clean on CI pending review.

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2021-03-30 17:34:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/588#pullrequestreview-624531718  

📁 include/boost/math/common_factor.hpp

```diff
  18 |+ BOOST_MATH_HEADER_DEPRECATED("<boost/integer/common_factor.hpp>");
  19 |+ #else
  20 |+ #error Common factor is not available in standalone mode because it requires boost.integer.
```

> Username: NAThompson  
> Created_at: 2021-03-30 17:34:17 UTC  
> Updated_at: 2021-03-30 18:44:05 UTC  
> Url: https://github.com/boostorg/math/pull/588#discussion_r604299406  

Isn't gcd part of the standard library now?

> Username: mborland  
> Created_at: 2021-03-30 18:26:41 UTC  
> Updated_at: 2021-03-30 18:44:05 UTC  
> Url: https://github.com/boostorg/math/pull/588#discussion_r604335485  

Yes; `std::gcd` was added in C++17.


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-30 17:38:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/588#pullrequestreview-624535972  

📁 include/boost/math/tools/polynomial_gcd.hpp

```diff
  63 |+ } // namespace gcd_detail
  64 |+ } // namespace boost::integer
  65 |+ #error polynomial gcd can only be used in standalone mode with C++17 or higher
```

> Username: jzmaddock  
> Created_at: 2021-03-30 17:38:58 UTC  
> Updated_at: 2021-03-30 18:44:05 UTC  
> Url: https://github.com/boostorg/math/pull/588#discussion_r604302724  

This #error appears to be misplaced - missing a #else branch?  
  
More pertinently, this header is included by polynomial.hpp, so we don't want hard #error's here, probably better to static_assert in it's interface somewhere if the gcd code is actually instantiated.  BTW I'm *reasonably* sure that static_assert's need to depend on the template parameter, if you do a `static_assert(false, "message")` then the compiler get get ultra-cute and realises that the assert will always trigger and issue an error even if the template is never instantiated.  That's why I've used `static_assert(sizeof(T) == 0, "message")` in the past, even though it's much less readable.


---

## Comment 4

> Username: mborland  
> Created_at: 2021-04-02 08:28:24 UTC  
> Url: https://github.com/boostorg/math/pull/588#issuecomment-812412698  

The CI system seems to have timed out on this one. Still shows it as in the queue, but all of the failures are: "This request was automatically failed because there were no enabled runners online to process the request for more than 1 days."

---

## Comment 5

> Username: mborland  
> Created_at: 2021-04-02 11:53:04 UTC  
> Url: https://github.com/boostorg/math/pull/588#issuecomment-812498031  

Consolidated into #597

---
