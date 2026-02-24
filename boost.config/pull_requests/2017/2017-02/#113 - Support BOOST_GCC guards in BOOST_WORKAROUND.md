# #113 Support BOOST_GCC guards in BOOST_WORKAROUND [Merged]

> Username: glenfe  
> Created at: 2017-02-02 18:47:53 UTC  
> Updated at: 2017-02-04 13:18:53 UTC  
> Merged at: 2017-02-04 13:18:53 UTC  
> Closed at: 2017-02-04 13:18:53 UTC  
> Url: https://github.com/boostorg/config/pull/113  

BOOST_GCC is absent among the convenience guards present for BOOST_WORKAROUND.

---

## Comment 1

> Username: glenfe  
> Created_at: 2017-02-04 06:31:18 UTC  
> Url: https://github.com/boostorg/config/pull/113#issuecomment-277423199  

There are guards in workaround.hpp for BOOST_INTEL and BOOST_MSVC (among other macros). This adds guards for BOOST_GCC as well so that we do not see warnings when compiling with -Wundef.

---
