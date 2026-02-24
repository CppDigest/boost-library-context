# #47 Add find() variants. [Merged]

> Username: tzlaine  
> Created at: 2018-05-13 21:06:11 UTC  
> Updated at: 2018-05-18 14:37:56 UTC  
> Merged at: 2018-05-18 14:37:37 UTC  
> Closed at: 2018-05-18 14:37:37 UTC  
> Url: https://github.com/boostorg/algorithm/pull/47  



---

## Comment 1

> Username: tzlaine  
> Created_at: 2018-05-13 21:10:43 UTC  
> Url: https://github.com/boostorg/algorithm/pull/47#issuecomment-388656242  

Lemme know if you want me to drop the .travis.yml bit.  If you like the idea of CI for Boost.Algorithm, I'll add an Appveyor script for MSVC support.

---

## Comment 2

> Username: mclow  
> Created_at: 2018-05-14 18:43:05 UTC  
> Url: https://github.com/boostorg/algorithm/pull/47#issuecomment-388921151  

> `BidiIter find_backward(BidiIter first, BidiIter last, T const & x)`  
East const, Zach?  
  
I think I'd rather you proposed the CI stuff as a separate commit. That will keep the change log cleaner. I don't have a problem with the CI stuff, I'd just rather it was separated.

---

## Comment 3

> Username: tzlaine  
> Created_at: 2018-05-15 00:26:58 UTC  
> Url: https://github.com/boostorg/algorithm/pull/47#issuecomment-389004083  

There, how's that?  I'll prepare an Appveyor script too before submitting the CI piece.

---

## Comment 4

> Username: mclow  
> Created_at: 2018-05-18 14:37:55 UTC  
> Url: https://github.com/boostorg/algorithm/pull/47#issuecomment-390227982  

Thanks. Please watch the test bots; and ping me in a week or so to merge to master.

---
