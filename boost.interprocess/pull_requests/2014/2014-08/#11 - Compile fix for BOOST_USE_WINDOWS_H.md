# #11 Compile fix for BOOST_USE_WINDOWS_H [Merged]

> Username: MarcelRaad  
> Created at: 2014-08-25 15:04:46 UTC  
> Updated at: 2014-09-26 09:16:23 UTC  
> Merged at: 2014-08-31 19:40:00 UTC  
> Closed at: 2014-08-31 19:40:00 UTC  
> Url: https://github.com/boostorg/interprocess/pull/11  

With BOOST_USE_WINDOWS_H defined, get_process_times must be explicitly prefixed with the winapi namespace because ADL cannot deduce it.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-08-31 19:42:10 UTC  
> Url: https://github.com/boostorg/interprocess/pull/11#issuecomment-53999005  

Thanks for the patch. Let me know if you find more BOOST_USE_WINDOWS_H problems.

---
