# #2471 - Remove unused dependencies in build scripts [Closed]

> Username: alandefreitas  
> Created at: 2022-06-22 19:01:41 UTC  
> Updated at: 2024-11-25 17:06:52 UTC  
> Closed at: 2024-11-25 08:11:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2471  

The tests on s390x (#2389) failed because boost.coroutine doesn't work on s390x. To fix it (#2469) we need to conditionally remove the example targets that depend on coroutine.  
  
Both the CMake and B2 scripts are globally linking all targets (even interface targets) to all dependency targets instead of linking only to the targets they depend on.   
  
For instance, CMakeLists.txt [links](https://github.com/boostorg/beast/blob/34c4647caf197d1a24ef21ba34c363e4add336b2/CMakeLists.txt#L193) _everything_ to `Boost::coroutine` and Jamfile links all [tests](https://github.com/boostorg/beast/blob/34c4647caf197d1a24ef21ba34c363e4add336b2/test/Jamfile#L39) and many examples to boost.coroutine, even though only a few examples depend on it.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-11-25 17:06:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2471#issuecomment-2498574673  

better late than never I suppose
