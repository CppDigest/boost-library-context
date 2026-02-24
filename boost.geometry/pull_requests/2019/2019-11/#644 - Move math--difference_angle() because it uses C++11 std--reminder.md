# #644 Move math::difference_angle() because it uses C++11 std::reminder. [Merged]

> Username: awulkiew  
> Created at: 2019-11-18 17:50:11 UTC  
> Updated at: 2019-11-18 20:33:47 UTC  
> Merged at: 2019-11-18 20:33:47 UTC  
> Closed at: 2019-11-18 20:33:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/644  

Also add const& to math util arguments.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2019-11-18 19:37:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/644#pullrequestreview-318579667  

Thanks!  
  
@adl1995 any idea on the following:  
```Investigate why is this formula uses different signs than the one in GeographicLib```

---

## Comment 2

> Username: adl1995  
> Created_at: 2019-11-18 20:10:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/644#issuecomment-555188409  

As far as I can see, this function uses the same signs as in GeographicLib: https://sourceforge.net/p/geographiclib/code/ci/release/tree/include/GeographicLib/Math.hpp#l414

---

## Comment 3

> Username: awulkiew  
> Created_at: 2019-11-18 20:27:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/644#issuecomment-555194684  

@adl1995 Right, I was looking at the GeographicLib 1.46:  
https://sourceforge.net/p/geographiclib/code/ci/f1df2f5da8c05b8ec2fa484894a82861c87b7da3/tree/include/GeographicLib/Math.hpp#l475  
  
Thanks. I'll remove the comment.

---
