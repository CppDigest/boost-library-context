# #32 Add C++14-style _t aliases [Merged]

> Username: pdimov  
> Created at: 2017-05-16 20:56:52 UTC  
> Updated at: 2017-05-21 12:10:46 UTC  
> Merged at: 2017-05-18 18:47:38 UTC  
> Closed at: 2017-05-18 18:47:38 UTC  
> Url: https://github.com/boostorg/type_traits/pull/32  

This adds C++14-style _t aliases to the transformation traits. The best way to test these additions would probably be to change the transformation testing macros to automatically add the additional test for X##_t, but I'm not familiar enough with the type_traits testing infrastructure so I haven't done it.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-05-16 22:02:36 UTC  
> Url: https://github.com/boostorg/type_traits/pull/32#issuecomment-301928796  

Yes, please add this and why not the corresponding value `_v` for C++14 compilers for the next release. A lot of people is doing it.

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-05-17 21:35:03 UTC  
> Url: https://github.com/boostorg/type_traits/pull/32#issuecomment-302238379  

Added a simple test.

---
