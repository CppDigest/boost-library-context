# #174 return static_cast<result_type>(0) in round(). [Merged]

> Username: pulver  
> Created at: 2019-01-05 16:50:26 UTC  
> Updated at: 2019-01-14 18:46:27 UTC  
> Merged at: 2019-01-14 18:46:27 UTC  
> Closed at: 2019-01-14 18:46:27 UTC  
> Url: https://github.com/boostorg/math/pull/174  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-01-06 04:55:25 UTC  
> Url: https://github.com/boostorg/math/pull/174#issuecomment-451715366  

This "compiler is out of heap space" breaks every build. Looks like we're not the only ones with [this problem](https://github.com/appveyor/ci/issues/742) either.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2019-01-14 18:46:21 UTC  
> Url: https://github.com/boostorg/math/pull/174#issuecomment-454115950  

Test failure is spurious, merging.

---
