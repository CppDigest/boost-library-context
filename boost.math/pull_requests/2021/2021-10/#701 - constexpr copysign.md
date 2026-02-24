# #701 constexpr copysign [Merged]

> Username: mborland  
> Created at: 2021-10-09 07:06:56 UTC  
> Updated at: 2021-10-10 18:26:00 UTC  
> Merged at: 2021-10-10 17:38:26 UTC  
> Closed at: 2021-10-10 17:38:26 UTC  
> Url: https://github.com/boostorg/math/pull/701  

Implements: `copysign`, `copysignf`, and `copysignl`

---

## Comment 1

> Username: mborland  
> Created_at: 2021-10-10 08:52:59 UTC  
> Url: https://github.com/boostorg/math/pull/701#issuecomment-939433551  

@NAThompson This should be good to go. All the failures are related to the multiprecision issues in the `misc` test suite`.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2021-10-10 17:39:43 UTC  
> Url: https://github.com/boostorg/math/pull/701#issuecomment-939522301  

@mborland : The "Fix for mingw test failure" might have profitably been an amend commit. CI fix commits are uninteresting reading in the git log.

---

## Comment 3

> Username: mborland  
> Created_at: 2021-10-10 18:03:15 UTC  
> Url: https://github.com/boostorg/math/pull/701#issuecomment-939526035  

That's my mistake. I've been trying to keep these and the multi-precision ones cleaner. The log from the move to standalone here is not pretty.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-10-10 18:26:00 UTC  
> Url: https://github.com/boostorg/math/pull/701#issuecomment-939529903  

Lol, no worries. I'm pretty bad about it myself.

---
