# #942 Make real concept constexpr [Closed]

> Username: mborland  
> Created at: 2023-02-04 04:18:51 UTC  
> Updated at: 2023-02-05 22:32:37 UTC  
> Closed at: 2023-02-04 15:54:31 UTC  
> Url: https://github.com/boostorg/math/pull/942  

With C++14 we can have multi-line constexpr member functions so make `real_concept` support constexpr construction and operations. Should fix the build errors in #937.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-02-04 09:24:47 UTC  
> Url: https://github.com/boostorg/math/pull/942#issuecomment-1416706724  

Sorry wrong fix: we want real_concept to model our absolute minimal requirements, and there are number types (for example multiprecision types which reference C library backends such as MPFR) which can never be constepxr.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-02-04 15:54:31 UTC  
> Url: https://github.com/boostorg/math/pull/942#issuecomment-1416787298  

On an unrelated note if we are going to expand constexpr support in C++14 it might be worth dropping MSVC-14.0. It was unhappy in the PRs with tables, is doing the wrong thing here, and [from this pr](https://github.com/boostorg/math/pull/920) is missing support to full C++14. For now I think we are OK, but worth being aware of.  
  
@NAThompson for awareness

---

## Comment 3

> Username: NAThompson  
> Created_at: 2023-02-04 16:18:41 UTC  
> Url: https://github.com/boostorg/math/pull/942#issuecomment-1416792376  

@mborland , @jzmaddock : Does MSVC do autoupdates now? Certainly on Mac it is a task of some difficulty to stay on an old AppleClang version.

---

## Comment 4

> Username: mborland  
> Created_at: 2023-02-04 16:23:25 UTC  
> Url: https://github.com/boostorg/math/pull/942#issuecomment-1416793297  

You can still download old versions on the website https://visualstudio.microsoft.com/vs/older-downloads/. To get an older Apple Clang build I have to login with an Apple Developer account, and it still tries to update every time you launch. This was a problem when Ventura was released but the toolchains hadn't been updated yet...

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2023-02-04 18:04:38 UTC  
> Url: https://github.com/boostorg/math/pull/942#issuecomment-1416814255  

>  if we are going to expand `constexpr` support in C++14 it might be worth dropping MSVC-14.0  
  
MSVC-14.0 cannot handle C++14 `constexpr` reliably. If you would like to use the acutal word `constexpr` without workarounds and within a C++14-compatible environment, the MSVC-14.0 must be dropped.

---
