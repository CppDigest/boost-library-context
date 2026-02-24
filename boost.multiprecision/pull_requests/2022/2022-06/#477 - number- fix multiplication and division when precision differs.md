# #477 number: fix multiplication and division when precision differs [Merged]

> Username: mniestroj  
> Created at: 2022-06-23 10:25:29 UTC  
> Updated at: 2022-06-24 19:54:26 UTC  
> Merged at: 2022-06-24 17:10:50 UTC  
> Closed at: 2022-06-24 17:10:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/477  

This is clearly a copy-paste bug, as there is addition in both places  
now. Fix that.  
  
Fixes: 26ab08df2ed0 ("Test and fix mixed precision arithmetic with  
  component types.")

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-06-23 10:34:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/477#issuecomment-1164247464  

Ugh, that's horrible, thanks for catching this!

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2022-06-23 10:45:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/477#issuecomment-1164256954  

Nice catch! Thank you.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-06-24 17:10:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/477#issuecomment-1165769794  

Failing tests are caused by an unrelated change from gcc-8 mingw to gcc-11.2 mingw.  Strangely I'm unable to reproduce locally, and it looks a lot like a broken install (or binary path?) on the testing image, but it's very hard to tell.  In any case, I'm going to go ahead and merge while we figure this out.  
  
We should also try to add some tests for this issue.

---
