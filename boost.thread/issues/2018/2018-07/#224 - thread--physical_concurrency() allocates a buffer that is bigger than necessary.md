# #224 - thread::physical_concurrency() allocates a buffer that is bigger than necessary [Closed]

> Username: Remi-Coulom  
> Created at: 2018-07-15 16:37:41 UTC  
> Updated at: 2018-09-06 05:12:34 UTC  
> Closed at: 2018-09-06 05:12:34 UTC  
> Url: https://github.com/boostorg/thread/issues/224  

`buffer(size);` should be `buffer(size / sizeof(SYSTEM_LOGICAL_PROCESSOR_INFORMATION))`  
  
https://github.com/boostorg/thread/blob/develop/src/win32/thread.cpp  
  
On line 526

---

## Comment 1

> Username: viboes  
> Created at: 2018-08-06 11:49:13 UTC  
> Url: https://github.com/boostorg/thread/issues/224#issuecomment-410682039  

Thanks fror this report.  
  
PR is welcome.

---

## Comment 2

> Username: viboes  
> Created at: 2018-08-07 12:20:32 UTC  
> Url: https://github.com/boostorg/thread/issues/224#issuecomment-411036705  

https://github.com/boostorg/thread/commit/de7608f06730e1802958d4b0b03f378c08fc8bb1
