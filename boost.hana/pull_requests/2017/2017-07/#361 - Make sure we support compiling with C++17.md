# #361 Make sure we support compiling with C++17 [Merged]

> Username: ldionne  
> Created at: 2017-07-08 22:19:13 UTC  
> Updated at: 2017-07-22 23:38:00 UTC  
> Merged at: 2017-07-22 23:37:57 UTC  
> Closed at: 2017-07-22 23:37:57 UTC  
> Url: https://github.com/boostorg/hana/pull/361  

That should work out of the box, but (at least) Clang has some bugs that break us when using `-std=c++1z`.

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2017-07-08 22:50:47 UTC  
> Url: https://github.com/boostorg/hana/pull/361#issuecomment-313885595  

Is this complete? Github says checks failed but travis says the build is passing.  
  
I remember `make check` failing when I tried c++1z because of a bunch of additional warnings that caused it to fail. I don't see anything in the cmake stuff that addresses that.  
  
(or maybe I was just doing something else wrong but it was unrelated stuff... idk)  
  
In any case I am very excited about this :+1: :+1: :+1:

---

## Comment 2

> Username: ldionne  
> Created_at: 2017-07-09 15:48:28 UTC  
> Url: https://github.com/boostorg/hana/pull/361#issuecomment-313927845  

I just kicked off the Travis build now.

---
