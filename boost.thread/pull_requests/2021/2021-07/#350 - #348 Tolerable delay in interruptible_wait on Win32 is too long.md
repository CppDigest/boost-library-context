# #350 #348 Tolerable delay in interruptible_wait on Win32 is too long [Closed]

> Username: Dani-Hub  
> Created at: 2021-07-29 17:24:57 UTC  
> Updated at: 2021-10-26 13:58:29 UTC  
> Closed at: 2021-10-26 13:58:28 UTC  
> Url: https://github.com/boostorg/thread/pull/350  

Ensure that SetWaitableTimerEx's TolerableDelay parameter is clamped between 32 and 100 ms when computing the 5% deviation.

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-10-26 13:58:28 UTC  
> Url: https://github.com/boostorg/thread/pull/350#issuecomment-951968320  

Superseded by the merged #356 .

---
