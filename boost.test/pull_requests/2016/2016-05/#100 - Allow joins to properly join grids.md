# #100 Allow joins to properly join grids [Closed]

> Username: Houndie  
> Created at: 2016-05-19 14:07:03 UTC  
> Updated at: 2018-10-29 09:48:22 UTC  
> Closed at: 2018-10-29 09:48:22 UTC  
> Url: https://github.com/boostorg/test/pull/100  

Joining two grids (of the same arity) would formerly cause a compiler  
error.  
  
See Trac #12216

---

## Comment 1

> Username: Houndie  
> Created_at: 2016-05-19 14:08:08 UTC  
> Url: https://github.com/boostorg/test/pull/100#issuecomment-220335132  

Note that this pull request uses some c++14 features...I saw c++14 features used in grid.hpp (with auto return type) so I went ahead and used them myself.  If these changes are in places that support older standards, this pull request won't work as is...just let me know.

---

## Comment 2

> Username: Houndie  
> Created_at: 2016-05-20 18:45:51 UTC  
> Url: https://github.com/boostorg/test/pull/100#issuecomment-220687659  

This patch apparently causes some segfaults when running test cases compiled _without_ optimization.  I'll leave this patch up for inspirational purposes, but it might not work as is.

---

## Comment 3

> Username: Houndie  
> Created_at: 2016-05-23 20:16:39 UTC  
> Url: https://github.com/boostorg/test/pull/100#issuecomment-221083162  

Patch has been updated to no longer cause segmentation faults.  It still should only be considered "lightly tested".

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2016-09-05 22:04:04 UTC  
> Url: https://github.com/boostorg/test/pull/100#issuecomment-244815776  

Unfortunately your changes require C++14 and are breaking some other tests. So it will take some additional time to get it right.

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2018-10-29 09:48:19 UTC  
> Url: https://github.com/boostorg/test/pull/100#issuecomment-433847980  

Fixed by rev 2fc875b9b22c717e17e56171501bd9aa3051213c

---
