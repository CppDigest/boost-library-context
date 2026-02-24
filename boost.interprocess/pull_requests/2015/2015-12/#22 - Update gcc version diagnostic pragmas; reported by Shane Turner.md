# #22 Update gcc version diagnostic pragmas; reported by Shane Turner [Merged]

> Username: mclow  
> Created at: 2015-12-08 19:34:53 UTC  
> Updated at: 2015-12-08 19:40:35 UTC  
> Merged at: 2015-12-08 19:40:34 UTC  
> Closed at: 2015-12-08 19:40:34 UTC  
> Url: https://github.com/boostorg/interprocess/pull/22  

Shane wrote:  
  
> I found another issue with a bad compiler version check in boost/interprocess/detail/config_begin.hpp before using "#pragma GCC diagnostic push", which, for GCC, is only available at version 4.6.0 and later. Since I'm using GCC 4.4.7, my compile fails.  
  
Updated the version check to only fire on 4.6 and later.

---
