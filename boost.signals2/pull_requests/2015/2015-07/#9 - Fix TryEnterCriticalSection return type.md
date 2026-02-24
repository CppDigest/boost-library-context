# #9 Fix TryEnterCriticalSection return type [Merged]

> Username: MarcelRaad  
> Created at: 2015-07-09 07:05:20 UTC  
> Updated at: 2017-09-19 07:38:04 UTC  
> Merged at: 2015-07-09 18:06:58 UTC  
> Closed at: 2015-07-09 18:06:58 UTC  
> Url: https://github.com/boostorg/signals2/pull/9  

TryEnterCriticalSection returns a BOOL, which is a typedef for int.

---
