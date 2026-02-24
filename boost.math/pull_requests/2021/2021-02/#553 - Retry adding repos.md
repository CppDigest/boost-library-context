# #553 Retry adding repos [Merged]

> Username: mborland  
> Created at: 2021-02-23 19:16:45 UTC  
> Updated at: 2021-02-25 16:43:08 UTC  
> Merged at: 2021-02-24 18:07:15 UTC  
> Closed at: 2021-02-24 18:07:15 UTC  
> Url: https://github.com/boostorg/math/pull/553  

From https://github.com/boostorg/math/issues/549. There is currently [no official way](https://github.community/t/retry-for-failed-steps/17136) to retry steps so this adds extra steps that only run on failure.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-02-23 19:43:33 UTC  
> Url: https://github.com/boostorg/math/pull/553#issuecomment-784462845  

That's a neat solution - thanks for this Matt - hopefully we can cut down the churn a bit!

---

## Comment 2

> Username: mborland  
> Created_at: 2021-02-24 15:35:17 UTC  
> Url: https://github.com/boostorg/math/pull/553#issuecomment-785161712  

@jzmaddock This is green, but it does not look like any of the retry steps ran. At least we know that skipping those steps on success works.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-02-24 18:07:10 UTC  
> Url: https://github.com/boostorg/math/pull/553#issuecomment-785268209  

OK, lets merge and see!  Thanks for this.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-02-24 18:36:16 UTC  
> Url: https://github.com/boostorg/math/pull/553#issuecomment-785286026  

This is cool, ... I was having difficulty moulding and contorting my mindset to _didn't run is the new green_.

---
