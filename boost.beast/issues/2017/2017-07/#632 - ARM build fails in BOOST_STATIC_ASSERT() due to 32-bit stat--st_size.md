# #632 - ARM build fails in BOOST_STATIC_ASSERT() due to 32-bit stat::st_size [Closed]

> Username: ngladitz  
> Created at: 2017-07-10 06:55:31 UTC  
> Updated at: 2017-07-10 14:39:32 UTC  
> Closed at: 2017-07-10 14:39:32 UTC  
> Url: https://github.com/boostorg/beast/issues/632  

### Version of Beast  
74  
  
### Steps necessary to reproduce the problem  
```  
$ cat test.cpp  
#include <beast.hpp>  
  
$ arm-linux-gnueabi-g++ -c -I Beast/include/ -I boost_1_62_0/include/ test.cpp  
In file included from Beast/include/beast/config.hpp:12:0,  
                 from Beast/include/beast.hpp:11,  
                 from test.cpp:1:  
Beast/include/beast/core/impl/file_posix.ipp: In member function ‘uint64_t beast::file_posix::size(beast::error_code&) const’:  
boost_1_62_0/include/boost/static_assert.hpp:70:41: error: static assertion failed: sizeof(stat::st_size) == sizeof(std::uint64_t)  
 #     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
                                         ^  
Beast/include/beast/core/impl/file_posix.ipp:211:5: note: in expansion of macro ‘BOOST_STATIC_ASSERT’  
     BOOST_STATIC_ASSERT(  
     ^~~~~~~~~~~~~~~~~~~  
```  
  
### All relevant compiler information  
```  
$ arm-linux-gnueabi-g++ --version  
arm-linux-gnueabi-g++ (Ubuntu/Linaro 6.3.0-12ubuntu2) 6.3.0 20170406  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-10 13:41:51 UTC  
> Url: https://github.com/boostorg/beast/issues/632#issuecomment-314108850  

I think this means you have to have large file support turned on. I can remove the assert, I think it applies to NuDB not Beast.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-10 14:06:36 UTC  
> Url: https://github.com/boostorg/beast/issues/632#issuecomment-314116087  

I will get this into v77 right away and merge it
