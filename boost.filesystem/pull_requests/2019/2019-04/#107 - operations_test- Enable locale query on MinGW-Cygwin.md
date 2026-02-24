# #107 operations_test: Enable locale query on MinGW/Cygwin [Merged]

> Username: Kojoley  
> Created at: 2019-04-02 12:32:57 UTC  
> Updated at: 2019-04-14 17:49:35 UTC  
> Merged at: 2019-04-14 17:48:43 UTC  
> Closed at: 2019-04-14 17:48:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/107  

The `GetUserDefaultUILanguage` is available for a quite long time already (about 19 years for Cygwin, and 7-12 years for MinGW).  
  
Fixes test fails on non-EN system locales. More info on the symmetrical PR for System https://github.com/boostorg/system/pull/40

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-04-14 17:48:51 UTC  
> Url: https://github.com/boostorg/filesystem/pull/107#issuecomment-483027760  

Thanks.

---
