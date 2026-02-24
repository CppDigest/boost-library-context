# #76 Fix standalone build [Merged]

> Username: Flamefire  
> Created at: 2025-10-23 16:50:43 UTC  
> Updated at: 2025-10-24 11:47:54 UTC  
> Merged at: 2025-10-24 08:11:37 UTC  
> Closed at: 2025-10-24 08:11:37 UTC  
> Url: https://github.com/boostorg/static_string/pull/76  

The config.hpp uses `BOOST_LIBSTDCXX_VERSION` which isn't defined in  
standalone mode so `BOOST_STATIC_STRING_NO_CXX17_HDR_STRING_VIEW` will  
be defined.  
This then causes build failures for code expecting the availability of  
string_view.  
  
First fix the test failure by checking for BOOST_STATIC_STRING_HAS_ANY_STRING_VIEW before using a function that requires a string_view

---
