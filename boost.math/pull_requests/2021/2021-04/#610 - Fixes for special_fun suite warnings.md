# #610 Fixes for special_fun suite warnings [Merged]

> Username: mborland  
> Created at: 2021-04-21 18:41:11 UTC  
> Updated at: 2021-04-25 08:38:12 UTC  
> Merged at: 2021-04-23 17:17:07 UTC  
> Closed at: 2021-04-23 17:17:07 UTC  
> Url: https://github.com/boostorg/math/pull/610  

Several instances of `-Wdeprecated-copy` and `-Wunused-variable` plus one instance of `-Wimplicit-const-int-float-conversion`. I am sure there will be other PRs here and in Boost.Random to address instances of deprecated copy.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-04-22 19:11:45 UTC  
> Url: https://github.com/boostorg/math/pull/610#issuecomment-825115419  

@jzmaddock Pending further review this should be good to go.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-04-23 07:57:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/610#pullrequestreview-643027147  

📁 include/boost/math/special_functions/detail/hypergeometric_asym.hpp

```diff
  46 |         {
  47 |-            e = z > (std::numeric_limits<long long>::max)() ? (std::numeric_limits<long long>::max)() : lltrunc(z, pol);
  47 |+            e = z > static_cast<T>((std::numeric_limits<long long>::max)()) ? (std::numeric_limits<long long>::max)() : lltrunc(z, pol);
```

> Username: jzmaddock  
> Created_at: 2021-04-23 07:57:15 UTC  
> Updated_at: 2021-04-23 07:57:16 UTC  
> Url: https://github.com/boostorg/math/pull/610#discussion_r619018279  

What's the warning on this one?  
  
My only (very slight) concern, is that for multiprecision types it is in general cheaper to compare to an integer, than to create a temporary T and compare.  In this particular case I very much doubt that anyone will notice the difference in a hypergeometric calculation, but in general we need to be a bit careful about casting integers to T's just out of habit.

> Username: mborland  
> Created_at: 2021-04-23 15:30:49 UTC  
> Url: https://github.com/boostorg/math/pull/610#discussion_r619312421  

```  
../../../boost/math/special_functions/detail/hypergeometric_asym.hpp:47:21: warning: implicit conversion from 'long long' to 'double' changes value from 9223372036854775807 to 9223372036854775808 [-Wimplicit-const-int-float-conversion]  
           e = z > ((std::numeric_limits<long long>::max)()) ? (std::numeric_limits<long long>::max)() : lltrunc(z, pol);  
```

> Username: jzmaddock  
> Created_at: 2021-04-23 17:17:01 UTC  
> Url: https://github.com/boostorg/math/pull/610#discussion_r619379868  

I bet there are a few of those, lets ignore it for now and merge - thanks for this!


---
