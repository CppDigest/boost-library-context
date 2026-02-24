# #355 #348 Tolerable delay in interruptible_wait on Win32 is too long [Closed]

> Username: Dani-Hub  
> Created at: 2021-08-17 16:29:27 UTC  
> Updated at: 2021-08-17 17:36:07 UTC  
> Closed at: 2021-08-17 17:36:06 UTC  
> Url: https://github.com/boostorg/thread/pull/355  

Ensure that SetWaitableTimerEx's TolerableDelay parameter is clamped between 32 and 1000 ms when computing the 5% deviation.

---
