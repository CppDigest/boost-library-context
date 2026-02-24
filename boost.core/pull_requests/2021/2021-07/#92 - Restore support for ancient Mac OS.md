# #92 Restore support for ancient Mac OS [Merged]

> Username: evanmiller  
> Created at: 2021-07-01 20:36:58 UTC  
> Updated at: 2021-07-01 23:00:01 UTC  
> Merged at: 2021-07-01 22:59:50 UTC  
> Closed at: 2021-07-01 22:59:50 UTC  
> Url: https://github.com/boostorg/core/pull/92  

Mac OS 10.4 and earlier lack Availability.h, causing compilation to fail. Rearrange the macro logic to include Availability.h only if we already think there may be support for uncaught exceptions.

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-07-01 23:00:00 UTC  
> Url: https://github.com/boostorg/core/pull/92#issuecomment-872598681  

Thanks.

---
