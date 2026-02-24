# #1304 Try repair or remove two JSO tests [Merged]

> Username: ckormanyos  
> Created at: 2025-08-17 06:54:16 UTC  
> Updated at: 2025-08-17 11:59:44 UTC  
> Merged at: 2025-08-17 11:58:48 UTC  
> Closed at: 2025-08-17 11:58:48 UTC  
> Url: https://github.com/boostorg/math/pull/1304  

Based on discussion [here](https://github.com/boostorg/math/pull/1301#issuecomment-3193815058).

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-08-17 06:54:25 UTC  
> Updated_at: 2025-08-17 06:57:39 UTC  
> Url: https://github.com/boostorg/math/pull/1304#issuecomment-3194178283  

As a first step, explicitly activate precise floating-point for the runners.  
  
I was looking into the two failing tests at lines 59 and 60 and they purposely _throw_ (due to non-finite weights) when compiler variations of _fast_-_math_ are activated. So I am trying to explicitly set precise math on all the runners and see what happens.  
  
If this turns into a big hassle, we can always just eliminate the tests. Let's see.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-08-17 08:42:11 UTC  
> Updated_at: 2025-08-17 09:27:56 UTC  
> Url: https://github.com/boostorg/math/pull/1304#issuecomment-3194233273  

Older GCC compilers had a problem with one particular test-optimizer that did not explicitly name the trailing return type. This has been fixed.  
  
Originally in _develop_, all the GCC compilers had been disabled via compiler switch on a bunch of the tests (in the main test program). MSVC and clang were active. So I now activated all of GCC, clang and MSVC.  
  
Let's see if all the runners now work?

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-08-17 08:45:24 UTC  
> Updated_at: 2025-08-17 10:35:10 UTC  
> Url: https://github.com/boostorg/math/pull/1304#issuecomment-3194234876  

Intermediate checks on the branch [at Godbolt](https://godbolt.org/z/ezf6jWqM4).

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2025-08-17 10:28:26 UTC  
> Url: https://github.com/boostorg/math/pull/1304#issuecomment-3194292937  

Ok it's coming down to the wire with jSO. In the last run, curiously enough, the only failure was on GCC8 (having C++17, as required for `test_jso.cpp`). This on our CI, even though GCC 8 passed at Godbolt.  
  
So I'll disable _ONLY_ that compiler and see if there is any other random unexpected stuff on Drone.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2025-08-17 11:59:00 UTC  
> Updated_at: 2025-08-17 11:59:44 UTC  
> Url: https://github.com/boostorg/math/pull/1304#issuecomment-3194338180  

So this one runs green now across the board.  
  
- Initially, `jso_test.cpp` had limitations on GCC and only ran in its entirety on MSVC and clang.  
- It needed a little syntax clarification for old GCC compilers (even though the code was right, the older compilers did not get it).  
- Now the only exclusion for `jso_test.cpp` is GCC8 on Drone for the original two lines in question.  
  
So we have a lot more test coverage of `jso_test.cpp`.  
  
I'll push this one now and we should keep a watchful eye for a bit to ensure that jSO remains well-behaved in CI/CD.  
  
Cc: @NAThompson and @jzmaddock and @mborland

---
