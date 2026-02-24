# #210 Fix build on Embarcadero C++Builder 10.3 [Open]

> Username: tanzislam  
> Created at: 2019-03-03 22:03:34 UTC  
> Updated at: 2019-09-17 22:50:42 UTC  
> Url: https://github.com/boostorg/asio/pull/210  

Double braces aren't necessary for list-initialization after a '=' sign.  
  - https://en.cppreference.com/w/cpp/container/array#Example  
  - https://www.boost.org/doc/libs/1_69_0/doc/html/array/rationale.html  
  
In Embarcadero C++Builder 10.3 (`bcc32c`) that syntax fails to compile. Change to single braces.  
  
Also allow `BOOST_ASIO_HAS_HANDLER_HOOKS` to be enabled on the modern [Clang-based](http://docwiki.embarcadero.com/RADStudio/Rio/en/Win32_Clang-enhanced_Compilers) C++Builder compilers.

---
