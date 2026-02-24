# #51 Support removing the C++11 standard reference wrappers. [Merged]

> Username: Flast  
> Created at: 2015-02-03 16:47:55 UTC  
> Updated at: 2015-02-03 20:37:02 UTC  
> Merged at: 2015-02-03 20:37:02 UTC  
> Closed at: 2015-02-03 20:37:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/51  

This PR lets Fusion treat std::reference_wrapper as same as Boost.Ref.  
  
Tested under:  
- MSVC 9.0(2008) / 10.0(2010) / 11.0(2012u4) / 12.0(2013u4) / 14.0(2015 Preview)  
- GCC 4.9.2 C++03 / C++11 / C++1y  
- LLVM Clang 3.5.0 C++03 / C++11 / C++1y

---
