# #285 roots: Use boost::math::copysign [Merged]

> Username: neheb  
> Created at: 2019-12-20 02:41:45 UTC  
> Updated at: 2019-12-23 15:36:58 UTC  
> Merged at: 2019-12-23 15:20:36 UTC  
> Closed at: 2019-12-23 15:20:36 UTC  
> Url: https://github.com/boostorg/math/pull/285  

std::copysign is not available with uClibc-ng.  
  
Signed-off-by: Rosen Penev <rosenp@gmail.com>

---

## Comment 1

> Username: neheb  
> Created_at: 2019-12-21 04:05:56 UTC  
> Url: https://github.com/boostorg/math/pull/285#issuecomment-568150258  

Updated patch to use an if directive to select boost::math::copysign when std version is not available.

---

## Comment 2

> Username: neheb  
> Created_at: 2019-12-21 16:05:18 UTC  
> Url: https://github.com/boostorg/math/pull/285#issuecomment-568191981  

This succeeded compilation. Seems the buildbots have problems.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-12-21 16:08:54 UTC  
> Url: https://github.com/boostorg/math/pull/285#issuecomment-568192262  

@neheb : Just took a look at the build and the diffs; looks good to me. AppVeyor takes forever to run; presumably this is not time critical?

---

## Comment 4

> Username: neheb  
> Created_at: 2019-12-21 16:17:03 UTC  
> Url: https://github.com/boostorg/math/pull/285#issuecomment-568192818  

I'd like for this to make the next release of boost, but otherwise no.

---
