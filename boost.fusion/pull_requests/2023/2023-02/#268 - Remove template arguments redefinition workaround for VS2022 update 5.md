# #268 Remove template arguments redefinition workaround for VS2022 update 5 [Merged]

> Username: Lastique  
> Created at: 2023-02-27 15:51:42 UTC  
> Updated at: 2023-03-06 09:08:38 UTC  
> Merged at: 2023-02-27 23:29:50 UTC  
> Closed at: 2023-02-27 23:29:51 UTC  
> Url: https://github.com/boostorg/fusion/pull/268  

Update 5 adds `/Zc:templateScope` flag which causes compile errors in typedefs redefining template parameters.  
  
This fixes compilation errors of Boost.Log that were reported on boost-dev ML.

---

## Comment 1

> Username: Lastique  
> Created_at: 2023-03-01 23:57:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/268#issuecomment-1451050064  

@djowel Thanks. Please also merge to master so that it makes it into Boost 1.82.

---

## Comment 2

> Username: Lastique  
> Created_at: 2023-03-06 02:23:58 UTC  
> Url: https://github.com/boostorg/fusion/pull/268#issuecomment-1455333228  

I hate to be nagging but the deadline for merging to master is approaching...

---

## Comment 3

> Username: djowel  
> Created_at: 2023-03-06 02:42:06 UTC  
> Url: https://github.com/boostorg/fusion/pull/268#issuecomment-1455347426  

No worries. I appreciate your reminders. Merged.

---

## Comment 4

> Username: Lastique  
> Created_at: 2023-03-06 09:08:37 UTC  
> Url: https://github.com/boostorg/fusion/pull/268#issuecomment-1455749045  

Thank you.

---
