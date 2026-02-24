# #2459 - beasts buffers_adaptor leads to compile errors during request of Iterator to buffer [Closed]

> Username: h-bex  
> Created at: 2022-06-18 10:44:59 UTC  
> Updated at: 2024-02-06 20:41:15 UTC  
> Closed at: 2024-02-06 20:41:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2459  

### Version of Beast  
  
From `boost/beast/version.hpp`:  
``` c++  
#define BOOST_BEAST_VERSION 329  
```  
Boost Version: 1.79  
  
### Steps necessary to reproduce the problem  
  
Please use the following attached source code:  
[boostBug.zip](https://github.com/boostorg/beast/files/8933375/boostBug.zip)  
  
Compile (e.g. under MS VC compiler) with:  
```C:\PROGRA~1\MICROS~4\2022\ENTERP~1\VC\Tools\MSVC\1432~1.313\bin\Hostx64\x64\cl.exe  /nologo /TP -DBOOST_ASIO_NO_DEPRECATED=1 -DBOOST_ASIO_NO_TS_EXECUTORS=1 -DBOOST_BEAST_USE_STD_STRING_VIEW=1 -external:IC:\usr\include\boost-1_79 -external:W0 /DWIN32 /D_WINDOWS /EHsc /Zi /Ob0 /Od /RTC1 -MDd -sdl -utf-8 -permissive- -std:c++17 /showIncludes -c boostBug.cpp```  
or see under the following links:  
 - msvc: https://godbolt.org/z/rW56oc675  
 - clang: https://godbolt.org/z/zrz13rYzr  
 - gcc: https://godbolt.org/z/5beW5s36a  
  
### All relevant compiler information  
  
Compiler Versions:  
 - MSVC: Microsoft (R) C/C++ Optimizing Compiler Version 19.32.31329 for x64  
 - Clang: x86-64 clang 14.0.0  
 - GCC: x86-64 gcc 12.1  
 - Boost Version: 1.79  
  
#### Error Message (for GCC)  
  
```  
In file included from /opt/compiler-explorer/libs/boost_1_79_0/boost/asio.hpp:56,  
                 from <source>:7:  
/opt/compiler-explorer/libs/boost_1_79_0/boost/asio/buffers_iterator.hpp: In instantiation of 'struct boost::asio::detail::buffers_iterator_types<boost::beast::buffers_adaptor<boost::asio::mutable_buffer>::subrange<false>, char>':  
/opt/compiler-explorer/libs/boost_1_79_0/boost/asio/buffers_iterator.hpp:115:46:   required from 'class boost::asio::buffers_iterator<boost::beast::buffers_adaptor<boost::asio::mutable_buffer>::subrange<false>, char>'  
<source>:32:34:   required from 'void {anonymous}::consume(DynamicBuffer&) [with DynamicBuffer = boost::beast::buffers_adaptor<boost::asio::mutable_buffer>]'  
<source>:54:12:   required from here  
/opt/compiler-explorer/libs/boost_1_79_0/boost/asio/buffers_iterator.hpp:69:53: error: no type named 'const_iterator' in 'class boost::beast::buffers_adaptor<boost::asio::mutable_buffer>::subrange<false>'  
   69 |     typedef typename BufferSequence::const_iterator const_iterator;  
      |                                                     ^~~~~~~~~~~~~~  
Compiler returned: 1```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-18 15:00:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2459#issuecomment-1159479204  

congrats, you might be the first person to use this type :)
