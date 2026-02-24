# #556 The dinkumware library used by the Embarcadero clang-based compilers … [Merged]

> Username: eldiener  
> Created at: 2020-03-25 18:57:12 UTC  
> Updated at: 2021-10-02 21:13:24 UTC  
> Merged at: 2020-03-27 18:52:25 UTC  
> Closed at: 2020-03-27 18:52:25 UTC  
> Url: https://github.com/boostorg/build/pull/556  

…does not work with rtti turned off. I have reported this to Embarcadero as a potential bug but I strongly suspect that this is by design.

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-03-27 18:43:59 UTC  
> Url: https://github.com/boostorg/build/pull/556#issuecomment-605207570  

I have no idea what has failed even when looking at the "Details". All the tests run show that they have passed.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2020-03-27 18:51:56 UTC  
> Url: https://github.com/boostorg/build/pull/556#issuecomment-605210975  

Microsoft is removing a bunch of OS images this month, and next. It failed because the macOS images got forced canceled by them. And I haven't had time to remove them from the CI setup (busy with a deadline in a few days).

---
