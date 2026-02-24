# #618 Add MSVC /Gw switch [Merged]

> Username: Kojoley  
> Created at: 2020-06-09 21:30:07 UTC  
> Updated at: 2021-10-02 21:01:49 UTC  
> Merged at: 2020-06-10 22:54:18 UTC  
> Closed at: 2020-06-10 22:54:18 UTC  
> Url: https://github.com/boostorg/build/pull/618  



---

## Comment 1

> Username: grafikrobot  
> Created_at: 2020-06-10 21:44:51 UTC  
> Url: https://github.com/boostorg/build/pull/618#issuecomment-642284184  

The post makes it sound like it's a size optimization only. Why add the flag for speed optimize also?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2020-06-10 21:49:34 UTC  
> Url: https://github.com/boostorg/build/pull/618#issuecomment-642285918  

Why would you not want a size optimization that does not affect speed? Also, it is known that smaller executables perform better because of more cache hits.

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2020-06-10 22:53:21 UTC  
> Url: https://github.com/boostorg/build/pull/618#issuecomment-642308681  

I was more wondering if there was some other non-obvious reason :-)

---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:48 UTC  
> Url: https://github.com/boostorg/build/pull/618#issuecomment-932819962  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
