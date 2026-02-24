# #755 Fix for issue #754 [Closed]

> Username: mborland  
> Created at: 2022-02-02 19:42:02 UTC  
> Updated at: 2022-02-08 10:12:06 UTC  
> Closed at: 2022-02-04 19:02:24 UTC  
> Url: https://github.com/boostorg/math/pull/755  

@jzmaddock I saw your deduction guide post right before pushing this. I went with the SFINAE approach so we don't have to worry about potential broken support by compiler since C++11 is required.

---
