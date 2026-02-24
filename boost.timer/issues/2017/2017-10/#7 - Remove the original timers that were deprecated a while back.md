# #7 - Remove the original timers that were deprecated a while back [Open]

> Username: jeking3  
> Created at: 2017-10-23 16:24:26 UTC  
> Updated at: 2019-01-16 19:26:05 UTC  
> Url: https://github.com/boostorg/timer/issues/7  

Looking at http://www.boost.org/doc/libs/1_65_1/libs/timer/doc/index.html, it looks like there could be some code simplification by removing the original timers.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-01-16 18:37:27 UTC  
> Url: https://github.com/boostorg/timer/issues/7#issuecomment-454890883  

Good idea in principle. I've marked these headers as deprecated in https://github.com/boostorg/timer/commit/075d6b25ab185526b0704fc9d1c982931729c35c. Hopefully they would become removable in a few releases (there are still too many uses of them in tests, and one in Boost.Test.)

---

## Comment 2

> Username: jeking3  
> Created at: 2019-01-16 19:26:05 UTC  
> Url: https://github.com/boostorg/timer/issues/7#issuecomment-454907300  

It's been deprecated since at least 1.53.0:  
https://www.boost.org/doc/libs/1_53_0/libs/timer/doc/index.html  
  
I guess this is the next step, making it noisy....
