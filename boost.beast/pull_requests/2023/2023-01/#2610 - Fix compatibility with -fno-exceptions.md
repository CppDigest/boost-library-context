# #2610 Fix compatibility with -fno-exceptions [Closed]

> Username: bc-lee  
> Created at: 2023-01-03 07:50:32 UTC  
> Updated at: 2023-01-04 01:58:17 UTC  
> Closed at: 2023-01-04 01:58:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2610  

When I'm trying to compile a simple C++ code using boost/beast and clang with `-fno-exceptions` I got the following error. This patch is intended to fix these compilation errors.  
```  
/path/to/include/boost/beast/core/impl/basic_stream.hpp:152:5: error: cannot use 'try' with exceptions disabled  
    try  
  
/path/to/include/boost/beast/include/boost/beast/core/impl/buffers_cat.hpp:104:13: error: cannot use 'throw' with exceptions disabled  
            throw std::logic_error("");  
```

---

## Comment 1

> Username: bc-lee  
> Created_at: 2023-01-03 10:07:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2610#issuecomment-1369586843  

Oh, lots of CI failures. Sorry to hear that boost/beast requires `BOOST_ASIO_NO_DEPRECATED`. If that macro exists, there is no way to capture the error in a world without C++ exceptions. Feel free to close this PR.

---
