# #45 Fix gcc syntax warnings enabled with -Wall and -Wpedantic [Merged]

> Username: dnmiller  
> Created at: 2022-06-21 21:58:14 UTC  
> Updated at: 2022-06-22 02:20:24 UTC  
> Merged at: 2022-06-22 02:20:24 UTC  
> Closed at: 2022-06-22 02:20:24 UTC  
> Url: https://github.com/boostorg/leaf/pull/45  

Some minor syntax issues (missing braces and extra semicolons) cause GCC to complain with -Wpedantic enabled. This PR allows compilation with -Wpedantic.  
  
If you can give guidance on how to enable these flags in CI, I'll add them there as well.

---

## Comment 1

> Username: zajo  
> Created_at: 2022-06-21 22:43:27 UTC  
> Url: https://github.com/boostorg/leaf/pull/45#issuecomment-1162434775  

I'd rather not add it to the CI, I'm not a big fan of these warnings.  
  
I'll approve your pull request as soon as CI cycles through, thank you!

---
