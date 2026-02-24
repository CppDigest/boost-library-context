# #12 Fix incorrect on_exit() callback called on any child exited. [Merged]

> Username: egorpugin  
> Created at: 2017-07-23 21:15:43 UTC  
> Updated at: 2017-07-23 21:19:57 UTC  
> Merged at: 2017-07-23 21:18:39 UTC  
> Closed at: 2017-07-23 21:18:39 UTC  
> Url: https://github.com/boostorg/process/pull/12  

signal_set's async_wait is only called after successful fork.  
Add pid field to wait_handler.  
Use waitpid instead of wait. Now the callback is called when correct child is dead. Otherwise we reset async_wait.  
Add 3 tests for single ios, double ios and double threaded ios.  
Tested on ubuntu 16.04.  
  
I know it's a bit late, but can this fix to be shipped in 1.65.0?

---

## Comment 1

> Username: egorpugin  
> Created_at: 2017-07-23 21:16:15 UTC  
> Url: https://github.com/boostorg/process/pull/12#issuecomment-317282805  

Issue for reference https://github.com/klemens-morgenstern/boost-process/issues/109

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2017-07-23 21:18:44 UTC  
> Url: https://github.com/boostorg/process/pull/12#issuecomment-317282973  

Thanks man!

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2017-07-23 21:19:57 UTC  
> Url: https://github.com/boostorg/process/pull/12#issuecomment-317283050  

I'll pull it into master if it works at ci https://travis-ci.org/klemens-morgenstern/boost-process

---
