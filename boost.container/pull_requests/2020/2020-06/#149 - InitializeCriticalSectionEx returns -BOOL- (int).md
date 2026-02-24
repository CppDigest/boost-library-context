# #149 InitializeCriticalSectionEx returns "BOOL" (int) [Closed]

> Username: mrpi  
> Created at: 2020-06-08 10:37:19 UTC  
> Updated at: 2020-08-14 08:42:31 UTC  
> Closed at: 2020-07-12 10:44:39 UTC  
> Url: https://github.com/boostorg/container/pull/149  

Retcode of InitializeCriticalSectionEx was not correct:  
https://docs.microsoft.com/en-us/windows/win32/api/synchapi/nf-synchapi-initializecriticalsectionex

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2020-07-12 10:44:50 UTC  
> Url: https://github.com/boostorg/container/pull/149#issuecomment-657204996  

Many thanks for the report!

---
