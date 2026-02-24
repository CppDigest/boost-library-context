# #45 Port to Boost.WinAPI and cleanup warnings [Merged]

> Username: Lastique  
> Created at: 2017-08-28 17:14:12 UTC  
> Updated at: 2017-09-05 18:10:32 UTC  
> Merged at: 2017-08-28 19:14:24 UTC  
> Closed at: 2017-08-28 19:14:24 UTC  
> Url: https://github.com/boostorg/date_time/pull/45  

This PR ports Boost.DateTime to Boost.WinAPI for Windows-specific timing functions. This should resolve compilation issues with clang/gcc, where Windows SDK declares functions differently than Boost.DateTime.  
  
This PR also silences a few warnings about unused variables and possible integer truncation in tests.

---

## Comment 1

> Username: mclow  
> Created_at: 2017-08-28 18:26:54 UTC  
> Url: https://github.com/boostorg/date_time/pull/45#issuecomment-325437528  

The test cleanups all look fine.  What are the "compilation issues with clang/gcc"? that you're trying to resolve?

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-08-28 18:42:47 UTC  
> Url: https://github.com/boostorg/date_time/pull/45#issuecomment-325441927  

See the "[boost] [build][wave][thread][date-time] 1.65.0 clang on Windows build failures" topic on the ML.  
  
Boost.DateTime declares `extern "C"` functions from WinAPI differently than Windows SDK (i.e. the functions use types like `boost::date_time::winapi::FILETIME` instead of `::_FILETIME` in Windows SDK), which makes the compilers complain. This problem is solved in Boost.WinAPI.

---

## Comment 3

> Username: eldiener  
> Created_at: 2017-08-28 19:03:30 UTC  
> Url: https://github.com/boostorg/date_time/pull/45#issuecomment-325447624  

This looks good to me also. It does add the winapi dependency to date_time, but I think that is the right thing to do.

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-09-05 15:16:33 UTC  
> Url: https://github.com/boostorg/date_time/pull/45#issuecomment-327208365  

Could this PR be merged to master, please? Thanks.

---

## Comment 5

> Username: eldiener  
> Created_at: 2017-09-05 16:49:02 UTC  
> Url: https://github.com/boostorg/date_time/pull/45#issuecomment-327236177  

Have you checked that it has cycled through the development tests successfully yet ?

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-09-05 17:05:54 UTC  
> Url: https://github.com/boostorg/date_time/pull/45#issuecomment-327241251  

Yes, most of them have cycled (all the Windows ones have). I didn't find any failures caused by this change.

---

## Comment 7

> Username: eldiener  
> Created_at: 2017-09-05 18:10:32 UTC  
> Url: https://github.com/boostorg/date_time/pull/45#issuecomment-327257720  

I merged to master.

---
