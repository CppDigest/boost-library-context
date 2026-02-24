# #38 Fix BOOST_STATIC_ASSERT with template arguments [Merged]

> Username: tanzislam  
> Created at: 2019-10-25 14:34:13 UTC  
> Updated at: 2019-10-26 16:42:45 UTC  
> Merged at: 2019-10-25 21:06:07 UTC  
> Closed at: 2019-10-25 21:06:07 UTC  
> Url: https://github.com/boostorg/endian/pull/38  

a2025a932 introduced a `BOOST_STATIC_ASSERT`-check with a template argument list. This causes a compilation error in a pre-C++11 compiler without support for variadic arguments (i.e. if `BOOST_NO_CXX11_VARIADIC_MACROS` is set).  
  
For example:  
```bash  
$ echo '#include <boost/endian/buffers.hpp>' > test.cpp  
$ g++ -c -std=c++03 -DBOOST_NO_CXX11_VARIADIC_MACROS -I/path/to/boost test.cpp  
```  
  
results in:  
  
```  
In file included from /path/to/boost/boost/endian/detail/endian_store.hpp:9,  
                 from /path/to/boost/boost/endian/buffers.hpp:30,  
                 from test.cpp:1:  
/path/to/boost/boost/endian/detail/endian_reverse.hpp:111:76: error: macro "BOOST_STATIC_ASSERT" passed 2 arguments, but takes just 1  
  111 |     BOOST_STATIC_ASSERT( is_integral<T>::value && !is_same<T, bool>::value );  
      |                                                                            ^  
In file included from /path/to/boost/boost/endian/detail/endian_reverse.hpp:13,  
                 from /path/to/boost/boost/endian/detail/endian_store.hpp:9,  
                 from /path/to/boost/boost/endian/buffers.hpp:30,  
                 from test.cpp:1:  
/path/to/boost/boost/static_assert.hpp:157: note: macro "BOOST_STATIC_ASSERT" defined here  
  157 | #     define BOOST_STATIC_ASSERT( B ) \  
      |  
```

---
