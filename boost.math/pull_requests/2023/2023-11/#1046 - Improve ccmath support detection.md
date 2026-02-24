# #1046 Improve ccmath support detection. [Merged]

> Username: jzmaddock  
> Created at: 2023-11-05 19:32:53 UTC  
> Updated at: 2024-01-24 21:33:38 UTC  
> Merged at: 2023-11-07 08:53:48 UTC  
> Closed at: 2023-11-07 08:53:48 UTC  
> Url: https://github.com/boostorg/math/pull/1046  

See discussion in https://github.com/boostorg/math/pull/1045.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2023-11-05 20:42:32 UTC  
> Updated_at: 2023-11-05 20:42:54 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1793840284  

This post confirms that this fixes the client code which first detected the issue discussed toward to bottom of #1045.  
  
Thank you so much John (@jzmaddock) for such a quick response to even this trivial issue!

---

## Comment 2

> Username: mborland  
> Created_at: 2023-11-06 07:10:16 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1794202893  

Should we apply this logic to all of the headers? Isfinite has the [old logic](https://github.com/boostorg/math/blob/develop/include/boost/math/ccmath/isfinite.hpp#L19), and some have none at all.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2023-11-06 07:16:03 UTC  
> Updated_at: 2023-11-06 07:16:33 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1794209209  

> Should we apply this logic to all of the headers?  
  
This is, indeed, a slim area, ... Clients using MSVC language standard 14 or lower. Anyway, I think that seems sensible both to repair it and to repair it consistently.  
  
For more information, by sheer random chance, I was revitalizing a very old legacy project recently and just so happened to have MSVC 141 C++ standard 14 running on CI (otherwise I would not have seen this at all...).  
  
Anyway, the error message(s) I got were [here](https://github.com/ckormanyos/mp_cpp/actions/runs/6761699209/job/18376842057#step:7:94).  
  
There are errors on `isnan`, `isinf`, `abs` and `ldexp`. But these might be the ones that were simply _picked_ _up_ by MSVC in my particular instantiations.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2023-11-06 08:45:27 UTC  
> Updated_at: 2023-11-06 08:46:02 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1794328543  

Hi Matt (@mborland) I was thinking about this issue and noticed C++17 is not so far away from C++14 (the language standard supported by all of Math/Multiprecision).  
  
I suppose you won't really like this question but, ...  
  
- How far is `ccmath` let's say, _away_ _from_ C++14?  
- Could/should we backport it?  
- I could help backport it for 1.85 if this makes things easier or more clear...

---

## Comment 5

> Username: mborland  
> Created_at: 2023-11-06 08:52:38 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1794339749  

> Hi Matt (@mborland) I was thinking about this issue and noticed C++17 is not so far away from C++14 (the language standard supported by all of Math/Multiprecision).  
>   
> I suppose you won't really like this question but, ...  
>   
> * How far is `ccmath` let's say, _away_ _from_ C++14?  
> * Could/should we backport it?  
> * I could help backport it for 1.85 if this makes things easier or more clear...  
  
It's not particularly far away from C++14, but that is not the pressing issue. For it to work we require proper `__builtin_is_constant_evaluated` which is GCC >= 9, clang >= 9, or MSVC > 1925. All those compilers support C++17 just fine.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2023-11-06 08:54:14 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1794343529  

> It's not particularly far away from C++14, but that is not the pressing issue. For it to work we require proper `__builtin_is_constant_evaluated`  
  
Understood. Challenging, getting through the maze of compilers...

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2023-11-06 10:01:38 UTC  
> Updated_at: 2023-11-06 10:02:15 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1794468500  

```  
2023-11-06T09:50:49.0791974Z ../tools/process_perf_results.cpp:12:10: fatal error: boost/format.hpp: No such file or directory  
2023-11-06T09:50:49.0793363Z    12 | #include <boost/format.hpp>  
2023-11-06T09:50:49.0794313Z       |          ^~~~~~~~~~~~~~~~~~  
2023-11-06T09:50:49.0795175Z compilation terminated.  
```  
  
This is now creeping into CI on some standalone. Maybe more but that's all I saw at a quick glance.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2023-11-06 13:24:15 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1794818672  

WTF?  Nothing has changed, either here, or in boostdep or Boost.Format... rerunning just to see what happens!

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2023-11-06 13:30:53 UTC  
> Updated_at: 2023-11-06 13:31:16 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1794833000  

> WTF? Nothing has changed, either here, or in boostdep or Boost.Format... rerunning just to see what happens!  
  
If it runs, you might have hit something that I've always wondered about, non-atomic checkins in the modular-boost. I wonder if something like that _might_ have happened?  
  
Let's see.  
  
I don't even know if commits to the master project or develop are atomic. That would surprise me...

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2023-11-06 13:54:29 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1794876756  

The thing is still looking for `<boost/format.hpp>` for several exemplary CPP-Files.  
  
This file is present in 1.83.  
  
I wonder if this header has been either knowingly or mistakenly removed from Boost on Develop in preparation for 1.84?

---

## Comment 11

> Username: pdimov  
> Created_at: 2023-11-06 17:27:03 UTC  
> Updated_at: 2023-11-06 17:27:34 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1795579985  

In short, add `-I example -I tools` to the `depinst` invocation to tell it to scan these directories for dependencies (in addition to the default `include`, `src`, `test`).  
  
https://github.com/boostorg/boostdep/issues/20

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2023-11-07 10:34:59 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1798240296  

Awesome! Thanks John and Matt

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2023-11-07 10:43:45 UTC  
> Url: https://github.com/boostorg/math/pull/1046#issuecomment-1798254080  

It is running perfectly _in_ _the_ _field_ on my unrelated project that actually uses it on MSVC `std=c++14`. I just check on the runners for my unrelated project. Great!

---
