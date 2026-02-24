# #15 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74 [Merged]

> Username: eldiener  
> Created at: 2020-04-08 21:59:23 UTC  
> Updated at: 2020-04-10 01:29:17 UTC  
> Merged at: 2020-04-09 13:38:47 UTC  
> Closed at: 2020-04-09 13:38:48 UTC  
> Url: https://github.com/boostorg/tuple/pull/15  



---

## Comment 1

> Username: djowel  
> Created_at: 2020-04-08 22:06:38 UTC  
> Url: https://github.com/boostorg/tuple/pull/15#issuecomment-611219236  

What happens to the old __BORLANDC__? Will that not be supported anymore?

---

## Comment 2

> Username: eldiener  
> Created_at: 2020-04-09 04:06:55 UTC  
> Url: https://github.com/boostorg/tuple/pull/15#issuecomment-611316414  

__BORLANDC__ becomes BOOST_BORLANDC in my PRs, which means the old bcc32 compiler. Since the new clang-based compilers also predefine __BORLANDC__ ( as well as __CODEGEARC__ ) doing anything just based on __BORLANDC__ ( or __CODEGEARC__ ) will affect both the old and the new clang-based compilers, which is foolish since the new clang-based compilers are so much better and more C++ compliant than the old bcc32. As far as supporing the old bcc32 compiler I am not removing that support by changing __BORLANDC__ to BOOST_BORLANDC. But in reality the old bcc32 compiler has hardly been able to use very many Boost libraries, despite the __BORLANDC__ and __CODEGEARC__ workarounds from the past, for a long time now and I personally would not spend even a second of time trying to get a modern Boost library to actually work with the old bcc32 compiler. Most Boost libraries will work with the new clang-based compilers and I am working with those which have some problems to find workarounds and report compiler and/or RTL bugs tro Embarcadero. The tuple library works pretty good with the new clang-based compilers in my testing.

---

## Comment 3

> Username: djowel  
> Created_at: 2020-04-10 01:29:16 UTC  
> Url: https://github.com/boostorg/tuple/pull/15#issuecomment-611830926  

Thanks @eldiener !

---
