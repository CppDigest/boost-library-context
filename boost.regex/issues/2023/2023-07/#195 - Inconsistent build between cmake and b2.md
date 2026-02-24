# #195 - Inconsistent build between cmake and b2 [Closed]

> Username: marza-sergey  
> Created at: 2023-07-06 02:50:30 UTC  
> Updated at: 2023-07-08 08:39:49 UTC  
> Closed at: 2023-07-08 08:39:49 UTC  
> Url: https://github.com/boostorg/regex/issues/195  

I'm currently trying to build boost using cmake. According to [release notes](https://www.boost.org/doc/libs/1_82_0/libs/regex/doc/html/boost_regex/background/history.html) Boost.Regex should be header only since boost 1.77.0, but the [documentation still states that it should be built separately](https://www.boost.org/doc/libs/1_82_0/more/getting_started/unix-variants.html#header-only-libraries) and b2 still produces .so binaries.  
It seems that some of the projects still depend on shared libraries even for newer versions of boost.  
What is the correct behavior? Should it still be possible to build shared libraries with cmake?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-07-08 08:39:49 UTC  
> Url: https://github.com/boostorg/regex/issues/195#issuecomment-1626920664  

The regex shared library still exists but is for legacy C++03 projects only.  Support for it has been deprecated for over a year and will be removed shortly.  So I see no reason for CMake to build it, only for the support to removed again, probably in the next release.
