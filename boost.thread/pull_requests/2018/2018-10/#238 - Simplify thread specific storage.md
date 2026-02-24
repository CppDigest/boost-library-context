# #238 Simplify thread specific storage [Closed]

> Username: Kojoley  
> Created at: 2018-10-03 23:00:29 UTC  
> Updated at: 2018-10-06 02:09:55 UTC  
> Closed at: 2018-10-06 02:09:55 UTC  
> Url: https://github.com/boostorg/thread/pull/238  

I have used `shared_ptr` for storing user data directly and custom destructor to handle user supplied destructor and lack of `release` on `shared_ptr`.  
  
This can be further improved by replacing `shared_ptr` with `local_shared_ptr`.  
  
Fixes #236.

---

## Comment 1

> Username: viboes  
> Created_at: 2018-10-05 05:24:16 UTC  
> Url: https://github.com/boostorg/thread/pull/238#issuecomment-427249940  

What do you mean by "This can be further improved by replacing shared_ptr with local_shared_ptr."?

---

## Comment 2

> Username: viboes  
> Created_at: 2018-10-05 05:27:46 UTC  
> Url: https://github.com/boostorg/thread/pull/238#issuecomment-427250422  

Please, could you describe what is the problem you are trying to fix, how have you fixed it and how do you probe that it has been fixed?  
  
The PR is quite consequent. Do you believe that it could be possible to identify minor issues and minor fixes or refactorings that can be understood better?  
  
Thanks anyway for your interest and working on those PR, really.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-10-05 12:39:06 UTC  
> Url: https://github.com/boostorg/thread/pull/238#issuecomment-427351921  

Closed and opened to restart travis

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-10-06 02:09:55 UTC  
> Url: https://github.com/boostorg/thread/pull/238#issuecomment-427538205  

Nevermind, I found my solution worse than the current (it allocates on every reset), this could be fixed with `shared_ptr` aliasing, but I am not sure I want continue this.

---
