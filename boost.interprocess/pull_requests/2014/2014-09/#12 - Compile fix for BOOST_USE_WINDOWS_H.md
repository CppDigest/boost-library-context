# #12 Compile fix for BOOST_USE_WINDOWS_H [Merged]

> Username: MarcelRaad  
> Created at: 2014-09-26 09:16:23 UTC  
> Updated at: 2015-05-20 07:11:54 UTC  
> Merged at: 2014-10-04 09:33:38 UTC  
> Closed at: 2014-10-04 09:33:38 UTC  
> Url: https://github.com/boostorg/interprocess/pull/12  

If BOOST_USE_WINDOWS_H is defined, the call to get_system_info has to be qualified with an explicit namespace.  
  
I must have forgotten this in #11, sorry.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-10-04 09:33:43 UTC  
> Url: https://github.com/boostorg/interprocess/pull/12#issuecomment-57899714  

Thanks!

---
