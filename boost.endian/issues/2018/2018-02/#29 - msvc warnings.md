# #29 - msvc warnings [Closed]

> Username: vinniefalco  
> Created at: 2018-02-20 17:48:22 UTC  
> Updated at: 2019-04-28 13:34:37 UTC  
> Closed at: 2019-04-28 13:34:37 UTC  
> Url: https://github.com/boostorg/endian/issues/29  

```  
1>C:\Users\vinnie\src\boost\boost/endian/buffers.hpp(282): warning C4127: conditional expression is constant (compiling source file C:\Users\vinnie\src\boost\libs\beast\test\beast\websocket\stream.cpp)  
1>C:\Users\vinnie\src\boost\boost/endian/buffers.hpp(282): note: consider using 'if constexpr' statement instead (compiling source file C:\Users\vinnie\src\boost\libs\beast\test\beast\websocket\stream.cpp)  
1>C:\Users\vinnie\src\boost\boost/endian/buffers.hpp(409): note: see reference to function template instantiation 'T boost::endian::detail::load_little_endian<T,4>(const void *) noexcept' being compiled  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2018-02-20 17:54:19 UTC  
> Url: https://github.com/boostorg/endian/issues/29#issuecomment-367061895  

`if constexpr` is C++17 though.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-04-28 13:34:37 UTC  
> Url: https://github.com/boostorg/endian/issues/29#issuecomment-487379946  

This specific warning should be gone in the current develop.
