# #13 Set correct access rights. Fix windows build. [Merged]

> Username: egorpugin  
> Created at: 2017-07-23 21:50:50 UTC  
> Updated at: 2017-07-24 05:56:15 UTC  
> Merged at: 2017-07-24 05:23:24 UTC  
> Closed at: 2017-07-24 05:23:24 UTC  
> Url: https://github.com/boostorg/process/pull/13  

Accidentally set 755 flags.  
Add filesystem dep to fix windows build.

---

## Comment 1

> Username: egorpugin  
> Created_at: 2017-07-23 23:54:50 UTC  
> Url: https://github.com/boostorg/process/pull/13#issuecomment-317291448  

(Saw your CIs failed on develop branch with my changes.)  
Also I see `wait.cpp` test failed on ubuntu locally.   
Maybe you could look at the test suite for known failure patterns.  
It's a bit hard for me right now with one-day boost.build and little boost.process knowledge.

---

## Comment 2

> Username: egorpugin  
> Created_at: 2017-07-24 00:06:51 UTC  
> Url: https://github.com/boostorg/process/pull/13#issuecomment-317292267  

My thoughts about that failed `double_wait` test.  
https://github.com/boostorg/process/blob/develop/test/wait.cpp#L68  
  
I think it fails, because we receive only one notification from kernel about died child. For io service in async manner, for `c.wait()` in sync manner.  
From my pov using io service sets everything to async, so we should not call `c.wait()`.  
... or from signal_set handler we must somehow notify child that it can continue execution from inside `c.wait()` call.  
  
So, from design point: either test is incorrect or we must fix library.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2017-07-24 05:56:15 UTC  
> Url: https://github.com/boostorg/process/pull/13#issuecomment-317326340  

Fixed it, thanks!

---
