# #185 Fix UB in memmove [Merged]

> Username: AmesingFlank  
> Created at: 2023-03-27 17:43:51 UTC  
> Updated at: 2023-03-29 23:36:45 UTC  
> Merged at: 2023-03-28 18:39:45 UTC  
> Closed at: 2023-03-28 18:39:45 UTC  
> Url: https://github.com/boostorg/wave/pull/185  

boost::wave sometimes calls memmove with a nullptr destination and a 0 byte length. This is undefined behavior. This diff adds a check so that memmove is not called if the length is 0

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2023-03-27 18:00:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/185#pullrequestreview-1359607692  

LGTM, thanks!

---

## Comment 2

> Username: jefftrull  
> Created_at: 2023-03-27 18:41:22 UTC  
> Url: https://github.com/boostorg/wave/pull/185#issuecomment-1485684299  

Thanks! Do you have a testcase by any chance? Then we could add it to the regression tests.  
  
Also this fixes bug #182 :)

---

## Comment 3

> Username: AmesingFlank  
> Created_at: 2023-03-27 18:53:41 UTC  
> Url: https://github.com/boostorg/wave/pull/185#issuecomment-1485699425  

> Thanks! Do you have a testcase by any chance? Then we could add it to the regression tests.  
>   
> Also this fixes bug #182 :)  
  
I don't have a repro case that I can post here, unfortunately. Can we use the code mentioned in #182 as the regression test?

---

## Comment 4

> Username: jefftrull  
> Created_at: 2023-03-27 19:08:07 UTC  
> Url: https://github.com/boostorg/wave/pull/185#issuecomment-1485720195  

It will require some reduction, I think, but hopefully so.

---

## Comment 5

> Username: jefftrull  
> Created_at: 2023-03-27 19:09:07 UTC  
> Url: https://github.com/boostorg/wave/pull/185#issuecomment-1485722290  

I note with interest your employer :) Glad to know Wave is useful to them.

---

## Comment 6

> Username: jefftrull  
> Created_at: 2023-03-29 23:36:45 UTC  
> Url: https://github.com/boostorg/wave/pull/185#issuecomment-1489474149  

Thanks for your contribution, which will be in Boost 1.82 (releasing soon).

---
