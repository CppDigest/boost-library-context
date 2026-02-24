# #888 Remove using std::cbrt to fix build on platforms which don't support it. [Merged]

> Username: NAThompson  
> Created at: 2022-12-03 15:55:37 UTC  
> Updated at: 2022-12-05 15:12:05 UTC  
> Merged at: 2022-12-05 15:11:55 UTC  
> Closed at: 2022-12-05 15:11:55 UTC  
> Url: https://github.com/boostorg/math/pull/888  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2022-12-03 20:33:55 UTC  
> Url: https://github.com/boostorg/math/pull/888#issuecomment-1336253400  

@mborland : Could you review/merge?

---

## Review 2 [Commented]

> Username: mborland  
> Created_at: 2022-12-03 20:40:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/888#pullrequestreview-1203571958  

📁 include/boost/math/constants/calculate_constants.hpp

```diff
1014 |-    using std::cbrt;
1014 |    using std::sqrt;
1015 |    return (cbrt(9-sqrt(T(69))) + cbrt(9+sqrt(T(69))))/cbrt(T(18));
```

> Username: mborland  
> Created_at: 2022-12-03 20:40:15 UTC  
> Updated_at: 2022-12-03 20:40:42 UTC  
> Url: https://github.com/boostorg/math/pull/888#discussion_r1038869411  

If the target platform standard library is missing `cbrt` entirely will this change the behavior?

> Username: NAThompson  
> Created_at: 2022-12-03 21:05:51 UTC  
> Updated_at: 2022-12-03 21:05:52 UTC  
> Url: https://github.com/boostorg/math/pull/888#discussion_r1038871732  

We should only be in the `calculate_constants.hpp` file if the type `T` is wider than the precision of `long double`; so no behavior should be changed. This was the observation of @jzmaddock .

> Username: mborland  
> Created_at: 2022-12-03 21:10:57 UTC  
> Url: https://github.com/boostorg/math/pull/888#discussion_r1038872263  

Sorry, I mean't won't the parser hit the call to `cbrt` and fail instead of failing at `using std::cbrt`? I doubt uClibc++ would be able to compile Boost.MP so I am not sure why this would be instantiated either way.

> Username: NAThompson  
> Created_at: 2022-12-03 22:02:53 UTC  
> Updated_at: 2022-12-03 22:02:54 UTC  
> Url: https://github.com/boostorg/math/pull/888#discussion_r1038877106  

Wait . . . if it can't compile boost/multiprecision then shouldn't it have died much earlier in the chain?  
  
Ultimately I think you're right: This needs to be tested by the bug reporter.

> Username: mborland  
> Created_at: 2022-12-03 22:10:04 UTC  
> Url: https://github.com/boostorg/math/pull/888#discussion_r1038877732  

Ultimately I think this is going to boil down to a standard library compliance problem. We are moving to C++14 in the next release so I don't think it's worth trying to make this work for a implementation that does not even fully support C++11.


---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-12-04 09:26:17 UTC  
> Url: https://github.com/boostorg/math/pull/888#issuecomment-1336358717  

>Sorry, I mean't won't the parser hit the call to cbrt and fail instead of failing at using std::cbrt? I doubt uClibc++ would be able to compile Boost.MP so I am not sure why this would be instantiated either way.  
  
No, the call is dependent upon the type of the template parameter, so it will do ADL when/only if instantiated, and since this code is for UDT's only, there had better be a cbrt in the same namespace as the type.  In other words this change does no harm.

---
