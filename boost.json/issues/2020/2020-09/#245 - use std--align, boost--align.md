# #245 - use std::align, boost::align [Closed]

> Username: vinniefalco  
> Created at: 2020-09-01 22:27:57 UTC  
> Updated at: 2020-09-04 03:06:49 UTC  
> Closed at: 2020-09-04 03:06:49 UTC  
> Url: https://github.com/boostorg/json/issues/245  

We should use `std::align` or `boost::align` as needed, since those are portable.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-09-02 15:26:56 UTC  
> Url: https://github.com/boostorg/json/issues/245#issuecomment-685811768  

Is there any reason to even use `boost::align`? `std::align` is available in C++11

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-02 15:33:03 UTC  
> Updated at: 2020-09-02 15:33:27 UTC  
> Url: https://github.com/boostorg/json/issues/245#issuecomment-685815930  

> std::align is available in C++11  
  
That is true, but older clang and gcc stdlibs don't have it. Just take my code from here:  
https://github.com/CPPAlliance/json/blob/57d7eb8e7f4845f6d36a8693b5809d92cffbf843/include/boost/json/impl/static_resource.ipp#L26  
  
And put it in `<boost/json/detail/align.hpp>`, include it from both places, and use it.

---

## Comment 3

> Username: sdkrystian  
> Created at: 2020-09-03 15:18:00 UTC  
> Url: https://github.com/boostorg/json/issues/245#issuecomment-686563769  

@vinniefalco Should I get rid of `detail::align_up` too?
