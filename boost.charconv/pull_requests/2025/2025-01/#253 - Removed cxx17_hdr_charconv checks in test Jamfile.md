# #253 Removed cxx17_hdr_charconv checks in test Jamfile [Closed]

> Username: anarthal  
> Created at: 2025-01-03 16:10:25 UTC  
> Updated at: 2025-01-03 17:22:51 UTC  
> Closed at: 2025-01-03 17:22:50 UTC  
> Url: https://github.com/boostorg/charconv/pull/253  

Makes boost_test_jamfile pick the tests that were changed

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-01-03 16:11:40 UTC  
> Url: https://github.com/boostorg/charconv/pull/253#issuecomment-2569477734  

If I'm not mistaken (let's see if CIs agree), these checks are not necessary, because you're already ifdef-ing the code out in the source files. Removing the checks makes `boost_test_jamfile` pick the tests, which will now run in the CMake builds, too.

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-01-03 17:22:50 UTC  
> Url: https://github.com/boostorg/charconv/pull/253#issuecomment-2569570891  

Looks like there are some non-obvious things going on there, so I'll close this for now.

---
