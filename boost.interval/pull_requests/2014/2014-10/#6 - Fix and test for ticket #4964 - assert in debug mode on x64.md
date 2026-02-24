# #6 Fix and test for ticket #4964 - assert in debug mode on x64 [Closed]

> Username: fkonvick  
> Created at: 2014-10-23 13:09:16 UTC  
> Updated at: 2014-10-26 21:51:30 UTC  
> Closed at: 2014-10-26 21:51:30 UTC  
> Url: https://github.com/boostorg/interval/pull/6  

This fixes #4964 and adds a testcase which used to trigger the assert.  Note that the testcase only fails on x64 (address-model=64) in debug mode (and when the fix is not in place, obviously).  
I haven't had a chance to test this on ARM, but MSDN is clear on that this applies to ARM in the same way so the fix also applies to ARM.

---

## Comment 1

> Username: mclow  
> Created_at: 2014-10-24 20:02:11 UTC  
> Url: https://github.com/boostorg/interval/pull/6#issuecomment-60441344  

The code looks fine, but we pull to the "develop" branch, and then, after the tests have cycled, then merge to "master".

---

## Comment 2

> Username: fkonvick  
> Created_at: 2014-10-24 21:11:56 UTC  
> Updated_at: 2014-10-24 21:15:30 UTC  
> Url: https://github.com/boostorg/interval/pull/6#issuecomment-60449981  

Ah, I knew I’d do something wrong.  Should I create another pull request, then?

---

## Comment 3

> Username: mclow  
> Created_at: 2014-10-25 01:03:19 UTC  
> Url: https://github.com/boostorg/interval/pull/6#issuecomment-60466616  

Yes, please. You can close this one.

---
