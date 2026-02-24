# #2 Simplify boost_message [Closed]

> Username: Flamefire  
> Created at: 2019-12-22 15:28:58 UTC  
> Updated at: 2019-12-22 17:52:36 UTC  
> Closed at: 2019-12-22 17:24:39 UTC  
> Url: https://github.com/boostorg/cmake/pull/2  

You can use `ARGN` instead of the manual appending.  
  
I also moved the check for the CMake version out and added a comment. This simplifies the code a bit and makes the intention clear (e.g. I didn't know about `--log-level` and got confused about the logic)

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-12-22 17:24:39 UTC  
> Url: https://github.com/boostorg/cmake/pull/2#issuecomment-568284439  

ARGN doesn't work well when the arguments are lists.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2019-12-22 17:36:53 UTC  
> Url: https://github.com/boostorg/cmake/pull/2#issuecomment-568285752  

Oh thanks, didn't know that. I thought ARGN was exactly for such use cases.  
  
Are you interested in the other part of the change? IMO it makes the function clearer and I can easily extract it

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-12-22 17:49:02 UTC  
> Url: https://github.com/boostorg/cmake/pull/2#issuecomment-568287363  

ARGN creates a list out of the arguments, and if they are themselves lists, like x;y z;w, ARGN is x;y;z;w and there's no way to recover the original lists.  
  
You might want to join the C++ Slack, https://cppalliance.org/slack/ - I ask stupid CMake questions in the #cmake channel there when I encounter an obstacle, and the ARGN case was one such occasion. :-)  
  
No, sorry, I'm not interested in refactorings.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2019-12-22 17:52:29 UTC  
> Updated_at: 2019-12-22 17:52:36 UTC  
> Url: https://github.com/boostorg/cmake/pull/2#issuecomment-568287852  

Ah good to know. I assumed it was some kind of intrinsic.  
  
Thanks for the suggestion, see you there!

---
