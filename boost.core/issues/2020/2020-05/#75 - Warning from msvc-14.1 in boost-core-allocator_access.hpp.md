# #75 - Warning from msvc-14.1 in boost/core/allocator_access.hpp [Closed]

> Username: HDembinski  
> Created at: 2020-05-24 09:59:38 UTC  
> Updated at: 2021-02-16 03:18:01 UTC  
> Closed at: 2020-05-24 14:35:32 UTC  
> Url: https://github.com/boostorg/core/issues/75  

This new warning was introduced on develop (breaking Boost Histogram CI tests):  
```  
C:\projects\boost\boost/core/allocator_access.hpp(453): warning C4996: 'std::allocator<T>::destroy': warning STL4010: Various members of std::allocator are deprecated in C++17. Use std::allocator_traits instead of accessing these members directly. You can define _SILENCE_CXX17_OLD_ALLOCATOR_MEMBERS_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2020-05-24 14:35:32 UTC  
> Url: https://github.com/boostorg/core/issues/75#issuecomment-633240296  

@HDembinski thanks; fixed in develop. We only used the members if we detect their existence, but MSVC 14.1 in /std:c++-latest provides the deprecated members on std::allocator<T> but warns when they're used. So it needed a specific workaround.

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-05-24 16:17:01 UTC  
> Url: https://github.com/boostorg/core/issues/75#issuecomment-633254481  

Thank you for the quick fix!

---

## Comment 3

> Username: daira  
> Created at: 2021-01-19 17:11:55 UTC  
> Url: https://github.com/boostorg/core/issues/75#issuecomment-762987454  

This also occurs on clang 11: https://github.com/zcash/zcash/pull/4951/commits/fa41e9adca9a485cd885ef08d636ab4930e4699c

---

## Comment 4

> Username: glenfe  
> Created at: 2021-02-16 03:18:01 UTC  
> Url: https://github.com/boostorg/core/issues/75#issuecomment-779554285  

@daira This should be addressed in the next (1.76) release: 86bff4c2d381c1832b6ff61fabe90635ff91d9fc
