# #654 Add GCC11 to CI [Merged]

> Username: mborland  
> Created at: 2021-07-02 07:03:53 UTC  
> Updated at: 2021-07-04 18:39:59 UTC  
> Merged at: 2021-07-04 17:36:00 UTC  
> Closed at: 2021-07-04 17:36:00 UTC  
> Url: https://github.com/boostorg/math/pull/654  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-07-02 16:55:45 UTC  
> Url: https://github.com/boostorg/math/pull/654#issuecomment-873133240  

While we're adding compilers to test, are there any which can be sensibly removed? We've got a really computationally expensive build  . . .

---

## Comment 2

> Username: mborland  
> Created_at: 2021-07-02 17:15:31 UTC  
> Url: https://github.com/boostorg/math/pull/654#issuecomment-873144481  

> While we're adding compilers to test, are there any which can be sensibly removed? We've got a really computationally expensive build . . .  
  
It looks like drone tests g++-6,8,10 and clang-10. We could run g++-5,7,9,11 and clang-5,6,7,8,9,11 using GHA. That would cut down a good bit. We could make further cuts and go odd versions numbers on GHA, and even numbers on drone. I do not know what the limitations for run time etc. are imposed by drone (if any).  
  
Thoughts @NAThompson and @jzmaddock?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-07-02 17:47:00 UTC  
> Url: https://github.com/boostorg/math/pull/654#issuecomment-873161505  

I'd probably get rid of gcc-5 and clang-5.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-07-04 17:35:53 UTC  
> Url: https://github.com/boostorg/math/pull/654#issuecomment-873630908  

Build failure was a timeout.

---
