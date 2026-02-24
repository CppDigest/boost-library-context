# #37 Enable use of __declspec(thread) for BOOST_ASIO_WINDOWS_APP. [Open]

> Username: fxb  
> Created at: 2016-04-27 11:52:42 UTC  
> Updated at: 2016-04-27 11:52:42 UTC  
> Url: https://github.com/boostorg/asio/pull/37  

This commit enables use of "__declspec(thread)" when targeting the Universal Windows Platform.  
  
Linking with Boost.Asio in a Universal Windows App currently fails, since thread local storage Win32 APIs (TlsAlloc, TlsFree, TlsGetValue and TlsSetValue) are not available yet. Even if those APIs are made available again in an upcoming Windows build, using the keyword is preferred.

---
