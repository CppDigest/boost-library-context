# #777 Fix logic error in roots.hpp [Merged]

> Username: jzmaddock  
> Created at: 2022-03-12 10:20:34 UTC  
> Updated at: 2024-01-24 21:34:02 UTC  
> Merged at: 2022-03-13 08:51:10 UTC  
> Closed at: 2022-03-13 08:51:11 UTC  
> Url: https://github.com/boostorg/math/pull/777  

Fixes https://github.com/boostorg/math/issues/776

---

## Comment 1

> Username: TFiFiE  
> Created_at: 2022-03-14 19:50:44 UTC  
> Url: https://github.com/boostorg/math/pull/777#issuecomment-1067222178  

This fix is itself somewhat problematic insofar that one can no longer reliably test whether the algorithm hit the iteration limit just by checking if the function changed `max_iter`. It can now also have been decremented by one.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-03-19 16:42:41 UTC  
> Url: https://github.com/boostorg/math/pull/777#issuecomment-1073041414  

>This fix is itself somewhat problematic insofar that one can no longer reliably test whether the algorithm hit the iteration limit just by checking if the function changed max_iter. It can now also have been decremented by one.  
  
I don't follow - the main loop will still decrement max_iter, the change is that now, if there are insufficient iterations left, then we just don't try the alternate bracketing algorithm.

---

## Comment 3

> Username: TFiFiE  
> Created_at: 2022-03-20 13:19:18 UTC  
> Url: https://github.com/boostorg/math/pull/777#issuecomment-1073250417  

Before, `max_iter` didn't get decremented if it ended up being responsible for stopping the algorithm, because `count` was guaranteed to be decremented at least once in `bracket_root_towards_min` and `bracket_root_towards_max`. Although this change might technically be correct, it is now no longer the case that `max_iter` being changed necessarily implies that it had no hand in stopping the algorithm.

---
