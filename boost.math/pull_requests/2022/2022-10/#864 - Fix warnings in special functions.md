# #864 Fix warnings in special functions [Merged]

> Username: mborland  
> Created at: 2022-10-31 01:07:33 UTC  
> Updated at: 2022-11-29 16:42:30 UTC  
> Merged at: 2022-11-29 16:42:26 UTC  
> Closed at: 2022-11-29 16:42:26 UTC  
> Url: https://github.com/boostorg/math/pull/864  



---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2022-11-04 17:32:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/864#pullrequestreview-1168945223  

📁 include/boost/math/special_functions/detail/hypergeometric_series.hpp

```diff
 241 |         int s1, s2;
 242 |-         T r = boost::math::lgamma(T(z + n), &s1, pol) - boost::math::lgamma(z, &s2, pol);
 242 |+         auto r = static_cast<T>(boost::math::lgamma(T(z + n), &s1, pol) - boost::math::lgamma(z, &s2, pol));
```

> Username: jzmaddock  
> Created_at: 2022-11-04 17:32:57 UTC  
> Url: https://github.com/boostorg/math/pull/864#discussion_r1014297261  

@mborland this diff all looks good to go to me, but just for future reference, I'm curious why this one warns as the results of the lgamma calls should be T's (even in the float case)?

> Username: mborland  
> Created_at: 2022-11-04 19:00:23 UTC  
> Updated_at: 2022-11-04 19:00:59 UTC  
> Url: https://github.com/boostorg/math/pull/864#discussion_r1014372657  

I assume `z` is an integer type so the call `boost::math::lgamma(z, &s2, pol)` is promoting to double. If that is a bad assumption I am not sure why, or if that was intentional.

> Username: jzmaddock  
> Created_at: 2022-11-10 18:37:21 UTC  
> Url: https://github.com/boostorg/math/pull/864#discussion_r1019489189  

No z is a real type.

> Username: jzmaddock  
> Created_at: 2022-11-10 18:37:52 UTC  
> Url: https://github.com/boostorg/math/pull/864#discussion_r1019489596  

Which compiler/test case was triggering the warnings?

> Username: mborland  
> Created_at: 2022-11-11 04:56:25 UTC  
> Updated_at: 2022-11-11 04:56:26 UTC  
> Url: https://github.com/boostorg/math/pull/864#discussion_r1019832403  

> Which compiler/test case was triggering the warnings?  
  
Example is [here](https://github.com/boostorg/math/actions/runs/3439561683/jobs/5737002658#step:11:837) using MSVC 2022 running `git_issue_810` with C++20.


---
