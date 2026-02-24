# #109 - std::allocator<void> deprecated in C++17 [Closed]

> Username: vinniefalco  
> Created at: 2018-05-09 18:50:14 UTC  
> Updated at: 2018-10-19 19:12:15 UTC  
> Closed at: 2018-10-19 19:12:15 UTC  
> Url: https://github.com/boostorg/asio/issues/109  

This warning appears when building with MSVC and `/std:c++17`  
  
1>c:\users\vinnie\lib\boost_1_67_0\boost\asio\associated_allocator.hpp(103): warning C4996: 'std::allocator<void>': warning STL4009: std::allocator<void> is deprecated in C++17. You can define _SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning.

---

## Comment 1

> Username: mq1n  
> Created at: 2018-09-23 09:39:01 UTC  
> Url: https://github.com/boostorg/asio/issues/109#issuecomment-423803860  

Is there any actual solution for it?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-10-19 19:12:15 UTC  
> Url: https://github.com/boostorg/asio/issues/109#issuecomment-431468321  

It looks like MSVC is doing the wrong thing here, see: https://github.com/chriskohlhoff/asio/issues/290#issuecomment-371867040
