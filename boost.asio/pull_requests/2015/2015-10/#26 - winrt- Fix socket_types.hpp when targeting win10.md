# #26 winrt: Fix socket_types.hpp when targeting win10 [Open]

> Username: mauve  
> Created at: 2015-10-13 10:03:51 UTC  
> Updated at: 2015-10-13 10:03:51 UTC  
> Url: https://github.com/boostorg/asio/pull/26  

when targeting win10 (`_WIN32_WINNT >= 0x0A00`) types such as `in_addr` and `in6_addr` are defined in the system headers so we should reuse them.

---
