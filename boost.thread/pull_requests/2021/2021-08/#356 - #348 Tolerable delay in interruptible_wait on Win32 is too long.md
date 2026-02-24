# #356 #348 Tolerable delay in interruptible_wait on Win32 is too long [Merged]

> Username: Dani-Hub  
> Created at: 2021-08-17 17:35:33 UTC  
> Updated at: 2021-08-27 17:17:00 UTC  
> Merged at: 2021-08-27 17:17:00 UTC  
> Closed at: 2021-08-27 17:17:00 UTC  
> Url: https://github.com/boostorg/thread/pull/356  

Ensure that SetWaitableTimerEx's TolerableDelay parameter is clamped between 32 and 1000 ms when computing the 5% deviation.

---

## Comment 1

> Username: ned14  
> Created_at: 2021-08-27 17:10:58 UTC  
> Url: https://github.com/boostorg/thread/pull/356#issuecomment-907349407  

Approved. Back to @pdimov

---
