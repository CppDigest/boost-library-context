# #195 Fix MinGW compilation problems [Closed]

> Username: Lastique  
> Created at: 2019-01-08 16:05:50 UTC  
> Updated at: 2019-02-08 17:24:18 UTC  
> Closed at: 2019-02-05 00:05:27 UTC  
> Url: https://github.com/boostorg/test/pull/195  

This fixes problems with MinGW gcc 5.3 that were discovered in Boost.Log CI [builds](https://ci.appveyor.com/project/Lastique/log/build/job/v59mk63a0swtdic1):  
  
- `master_test_suite` function redeclared without `dllimport`  
- `vsnprintf` not declared  
  
The latter problem I couldn't reproduce locally, but I guess it is caused by a missing include of stdio.h. If that doesn't help, another fix might be needed.

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-01-08 16:07:31 UTC  
> Url: https://github.com/boostorg/test/pull/195#issuecomment-452354122  

The `dllimport` problem is also fixed by https://github.com/boostorg/test/pull/190.

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2019-02-03 10:50:23 UTC  
> Url: https://github.com/boostorg/test/pull/195#issuecomment-460041292  

In next

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2019-02-05 00:05:27 UTC  
> Url: https://github.com/boostorg/test/pull/195#issuecomment-460463119  

Merged to develop, thanks

---
