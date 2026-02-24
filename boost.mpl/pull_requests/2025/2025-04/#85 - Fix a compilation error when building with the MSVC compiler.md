# #85 Fix a compilation error when building with the MSVC compiler [Closed]

> Username: yurybura  
> Created at: 2025-04-05 11:21:31 UTC  
> Updated at: 2025-07-03 03:47:02 UTC  
> Closed at: 2025-07-03 03:47:01 UTC  
> Url: https://github.com/boostorg/mpl/pull/85  

The `and` and `or` macros are still defined in the MSVC header <iso646.h> for C++ if Microsoft extensions are enabled. Microsoft extensions are enabled by default.  
  
Fixes #84

---

## Comment 1

> Username: nigels-com  
> Created_at: 2025-04-17 01:49:05 UTC  
> Url: https://github.com/boostorg/mpl/pull/85#issuecomment-2811515824  

Fixed our build-break on VS 2019 with boost 1.88.0.  
Much appreciated.

---

## Comment 2

> Username: jeking3  
> Created_at: 2025-07-03 03:47:02 UTC  
> Url: https://github.com/boostorg/mpl/pull/85#issuecomment-3030551911  

This was fixed in https://github.com/boostorg/mpl/commit/aa3b1c464d094c590ca443658800204ecaf80e6d.

---
