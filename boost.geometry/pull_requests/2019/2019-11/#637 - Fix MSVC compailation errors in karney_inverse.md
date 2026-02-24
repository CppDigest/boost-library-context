# #637 Fix MSVC compailation errors in karney_inverse. [Merged]

> Username: awulkiew  
> Created at: 2019-11-05 21:32:49 UTC  
> Updated at: 2019-11-06 12:45:03 UTC  
> Merged at: 2019-11-06 12:45:03 UTC  
> Closed at: 2019-11-06 12:45:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/637  

Plus warning fix.  
  
Even though this formula requires C++11 and is no longer included by default (see https://github.com/boostorg/geometry/pull/636) it is still tested (in `test/formulas/inverse.cpp`). Without this fix it does not compile with MSVC even when C++11 is supported.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2019-11-05 21:48:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/637#issuecomment-550036798  

Or should we disable testing of this formula as well?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2019-11-05 23:19:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/637#issuecomment-550067857  

I decided to put `karney_inverse` cases in a separate file to not loose the ability to run tests of other formulas without C++11 support.

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2019-11-06 08:27:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/637#pullrequestreview-312259010  

ok, thanks for this fix!

---
