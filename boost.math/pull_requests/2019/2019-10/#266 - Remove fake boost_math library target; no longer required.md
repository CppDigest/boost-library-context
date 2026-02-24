# #266 Remove fake boost_math library target; no longer required [Merged]

> Username: pdimov  
> Created at: 2019-10-24 17:17:11 UTC  
> Updated at: 2019-10-26 21:59:31 UTC  
> Merged at: 2019-10-26 21:59:24 UTC  
> Closed at: 2019-10-26 21:59:24 UTC  
> Url: https://github.com/boostorg/math/pull/266  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-10-24 21:48:31 UTC  
> Url: https://github.com/boostorg/math/pull/266#issuecomment-546117208  

Just read through the linked issue; seems like this is relatively low risk.  
  
@pdimov : Is it important that this go into 1.72?

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-10-24 22:09:32 UTC  
> Url: https://github.com/boostorg/math/pull/266#issuecomment-546123398  

It's not critical, but we gain nothing if it doesn't go into 1.72. In general we only discover installation/downstream issues when a release goes out, so delaying this for 1.73 won't help anyone.  
  
This basically reverts my earlier PR https://github.com/boostorg/math/pull/153.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-10-24 22:40:21 UTC  
> Url: https://github.com/boostorg/math/pull/266#issuecomment-546130972  

Ok cool. @jzmaddock : Cool with this getting merged to both develop and master as soon as the CI cycles?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-10-25 16:31:41 UTC  
> Url: https://github.com/boostorg/math/pull/266#issuecomment-546423041  

>Ok cool. @jzmaddock : Cool with this getting merged to both develop and master as soon as the CI cycles?  
  
Absolutely.

---
