# #320 fix inclusive() in integer axis [Merged]

> Username: HDembinski  
> Created at: 2021-04-24 10:10:00 UTC  
> Updated at: 2021-04-24 10:24:48 UTC  
> Merged at: 2021-04-24 10:24:47 UTC  
> Closed at: 2021-04-24 10:24:47 UTC  
> Url: https://github.com/boostorg/histogram/pull/320  

Closes #305

---

## Comment 1

> Username: HDembinski  
> Created_at: 2021-04-24 10:19:16 UTC  
> Updated_at: 2021-04-24 10:19:51 UTC  
> Url: https://github.com/boostorg/histogram/pull/320#issuecomment-826070574  

@henryiii I now finally fixed this. This was actually more severe than I originally thought. It was possible to trigger this bug, which would then trigger an assertion in debug mode. In release mode,  it would have probably caused a segfault.

---
