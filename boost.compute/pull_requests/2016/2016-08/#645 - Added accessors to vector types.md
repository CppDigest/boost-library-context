# #645 Added accessors to vector types [Closed]

> Username: amirshavit  
> Created at: 2016-08-21 11:27:10 UTC  
> Updated at: 2016-08-23 12:28:00 UTC  
> Closed at: 2016-08-23 12:22:54 UTC  
> Url: https://github.com/boostorg/compute/pull/645  

This basically replaces the protected m_values member with public x/y, x/y/z/w, s0..s7, s0..sf members according to the vector size.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-08-23 12:20:50 UTC  
> Url: https://github.com/boostorg/compute/pull/645#issuecomment-241713054  

I would recommend splitting this into two separate pull request: vector accessors and OpenCL version macros (fix for `nullary_kernel()` can go anywhere). Also, it would be great if you rebase it on `develop` branch and resolve conflicts.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2016-08-23 12:28:00 UTC  
> Url: https://github.com/boostorg/compute/pull/645#issuecomment-241714539  

Yeah, and I recommend doing it on separate branches. Using `master` of your fork for pull requests is not a good idea, it should be always equal to `master` branch of the repo it is forked from. The same comes for the `develop` branch. See https://help.github.com/articles/syncing-a-fork/ (you need to sync `develop` branch too).

---
