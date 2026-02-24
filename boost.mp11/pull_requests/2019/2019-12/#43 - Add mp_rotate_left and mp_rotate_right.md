# #43 Add mp_rotate_left and mp_rotate_right [Merged]

> Username: D-Barber  
> Created at: 2019-12-21 23:11:22 UTC  
> Updated at: 2020-01-21 19:59:29 UTC  
> Merged at: 2020-01-18 22:40:00 UTC  
> Closed at: 2020-01-18 22:40:00 UTC  
> Url: https://github.com/boostorg/mp11/pull/43  

I used mp11 to implement a list left rotation algorithm a while ago and having recently had cause to use it again (and extend it to cover some edge cases in my original implementaion) I thought I'd submit it here as a possible extension to the library. The implementation here is largely dictated by the SFINAE deficiencies of the Visual Studio 2013 compiler, as several versions of an alternative SFINAE-based implementation were fine for all the other test configurations. If you feel the fundamental idea has merit but I've missed a way to leverage the library more heavily to simplify the implementation then I'll happily put more time into making changes to it!

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-01-18 22:41:36 UTC  
> Url: https://github.com/boostorg/mp11/pull/43#issuecomment-575944863  

Thanks. I removed the default argument and simplified the implementation a bit. This foregoes the 0 optimization, but that's not going to be a common case. One could have gone either way on that.

---

## Comment 2

> Username: D-Barber  
> Created_at: 2020-01-21 19:59:29 UTC  
> Url: https://github.com/boostorg/mp11/pull/43#issuecomment-576854740  

Thanks @pdimov. Removing the optimisation makes sense, it significantly simplifies the implementation.

---
