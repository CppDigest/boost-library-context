# #82 make valgrind return error code on failure [Merged]

> Username: jeking3  
> Created at: 2018-05-15 00:35:46 UTC  
> Updated at: 2018-05-16 01:16:12 UTC  
> Merged at: 2018-05-16 01:16:10 UTC  
> Closed at: 2018-05-16 01:16:10 UTC  
> Url: https://github.com/boostorg/date_time/pull/82  



---

## Comment 1

> Username: mclow  
> Created_at: 2018-05-15 01:21:07 UTC  
> Url: https://github.com/boostorg/date_time/pull/82#issuecomment-389012130  

Looks good to me.

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-05-15 03:15:27 UTC  
> Url: https://github.com/boostorg/date_time/pull/82#issuecomment-389029789  

The MSVC2010 build error in CI is caused by https://github.com/boostorg/serialization/issues/107 - this can be ignored here.

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-05-16 01:15:58 UTC  
> Url: https://github.com/boostorg/date_time/pull/82#issuecomment-389363592  

Build failure is a known issue: https://github.com/boostorg/serialization/issues/107

---
