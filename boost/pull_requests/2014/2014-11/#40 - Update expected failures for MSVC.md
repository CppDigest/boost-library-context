# #40 Update expected failures for MSVC [Merged]

> Username: MarcelRaad  
> Created at: 2014-11-03 10:02:28 UTC  
> Updated at: 2015-01-05 20:16:44 UTC  
> Merged at: 2015-01-05 20:11:58 UTC  
> Closed at: 2015-01-05 20:11:58 UTC  
> Url: https://github.com/boostorg/boost/pull/40  

The tests that are incompatible with the C++11 auto keyword and promote_enum_msvc_bug_test also fail on all newer versions of MSVC.

---

## Comment 1

> Username: danieljames  
> Created_at: 2015-01-05 20:00:43 UTC  
> Url: https://github.com/boostorg/boost/pull/40#issuecomment-68767945  

Does this apply to msvc-13?

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2015-01-05 20:08:01 UTC  
> Url: https://github.com/boostorg/boost/pull/40#issuecomment-68769619  

Microsoft skipped version 13, so this updates the expected failures for MSVC 12 (2013) and 14 (2014 CTP / 2015).

---
