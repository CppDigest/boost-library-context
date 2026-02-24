# #219 Disable from_exception on cygwin. [Merged]

> Username: corngood  
> Created at: 2025-12-18 14:40:48 UTC  
> Updated at: 2025-12-23 15:51:23 UTC  
> Merged at: 2025-12-23 15:51:13 UTC  
> Closed at: 2025-12-23 15:51:13 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/219  

Cygwin doesn't have RTDL_NEXT, which causes a compile error with the non-windows implementation.  I'm not sure which implementation would be better to use, so just disable it for now.  
  
This is part of a set of changes to fix boost on cygwin.  You can see the other submodules that will require changes here:  
  
https://github.com/boostorg/boost/compare/master...corngood:boost:cygwin  
  
These are based on the out-of-date downstream patches in cygwin:  
  
https://cygwin.com/cgit/cygwin-packages/boost/tree/

---

## Comment 1

> Username: coveralls  
> Created_at: 2025-12-22 17:01:41 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/219#issuecomment-3682933560  

[![Coverage Status](https://coveralls.io/builds/77071882/badge)](https://coveralls.io/builds/77071882)  
  
coverage: 86.35%. remained the same  
when pulling **1572feb46a0c905182f99559521b1d1b372b3bc4 on corngood:cygwin**  
into **6ef20c3ab785200124e6dd6ee189e3469cc8a81b on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2025-12-23 15:51:23 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/219#issuecomment-3687107863  

Many thanks for the PR!

---
