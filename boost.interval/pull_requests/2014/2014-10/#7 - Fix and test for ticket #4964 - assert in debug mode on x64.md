# #7 Fix and test for ticket #4964 - assert in debug mode on x64 [Merged]

> Username: fkonvick  
> Created at: 2014-10-26 22:33:35 UTC  
> Updated at: 2014-11-07 09:37:24 UTC  
> Merged at: 2014-10-27 15:24:32 UTC  
> Closed at: 2014-10-27 15:24:32 UTC  
> Url: https://github.com/boostorg/interval/pull/7  

[This is a new version of the ex-pull request #6; this new one is set up for the develop branch.]  
  
This fixes #4964 and adds a testcase which used to trigger the assert. Note that the testcase only fails on x64 (address-model=64) in debug mode on Windows (and when the fix is not in place, obviously).  
I haven't had a chance to test this on ARM, but MSDN is clear on that this applies to ARM in the same way so the fix also applies to that platform.

---

## Comment 1

> Username: mclow  
> Created_at: 2014-10-27 15:24:36 UTC  
> Url: https://github.com/boostorg/interval/pull/7#issuecomment-60611240  

This looks good to me. Please ping me in a couple days (after the tests have cycled), and I will merge to the master branch as well.

---

## Comment 2

> Username: fkonvick  
> Created_at: 2014-11-07 09:37:24 UTC  
> Url: https://github.com/boostorg/interval/pull/7#issuecomment-62118991  

Ping :)

---
