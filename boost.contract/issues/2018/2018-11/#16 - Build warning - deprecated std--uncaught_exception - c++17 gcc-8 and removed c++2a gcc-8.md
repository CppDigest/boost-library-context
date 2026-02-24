# #16 - Build warning - deprecated std::uncaught_exception - c++17 gcc-8 and removed c++2a gcc-8 [Closed]

> Username: jeking3  
> Created at: 2018-11-04 22:28:22 UTC  
> Updated at: 2019-01-06 15:49:42 UTC  
> Closed at: 2019-01-06 15:49:42 UTC  
> Url: https://github.com/boostorg/contract/issues/16  

This warning appears so many times it causes the log to be too long, and Travis stops the job.  
Reference: https://en.cppreference.com/w/cpp/error/uncaught_exception  
Log: https://api.travis-ci.org/v3/job/450610135/log.txt  
```  
../../boost/contract/detail/operation/public_function.hpp:144:43: warning: â€˜bool std::uncaught_exception()â€™ is deprecated [-Wdeprecated-declarations]  
                 if(std::uncaught_exception()) {  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~  
In file included from /usr/include/c++/8/ios:39,  
                 from /usr/include/c++/8/ostream:38,  
                 from /usr/include/c++/8/iterator:64,  
                 from ../../boost/iterator/iterator_traits.hpp:10,  
                 from ../../boost/range/iterator_range_core.hpp:26,  
                 from ../../boost/range/iterator_range.hpp:13,  
                 from ../../boost/iostreams/traits.hpp:38,  
                 from ../../boost/iostreams/detail/call_traits.hpp:15,  
                 from ../../boost/iostreams/detail/adapter/device_adapter.hpp:22,  
                 from ../../boost/iostreams/tee.hpp:18,  
                 from ../../libs/contract/test/public_function/../detail/oteststream.hpp:10,  
                 from ../../libs/contract/test/public_function/old_virtual.cpp:9:  
/usr/include/c++/8/exception:102:8: note: declared here  
   bool uncaught_exception() _GLIBCXX_USE_NOEXCEPT __attribute__ ((__pure__));  
        ^~~~~~~~~~~~~~~~~~  
In file included from ../../boost/contract/public_function.hpp:32,  
                 from ../../libs/contract/test/public_function/old_virtual.cpp:10:  
../../boost/contract/detail/operation/public_function.hpp: In instantiation of â€˜void boost::contract::detail::public_function<O, VR, F, C, Args>::init() [with O = b::override_f; VR =   
```

---

## Comment 1

> Username: lcaminiti  
> Created at: 2018-11-05 16:54:09 UTC  
> Url: https://github.com/boostorg/contract/issues/16#issuecomment-435949136  

I'll fix this in one of the next released of Boost using __cpp_lib_uncaught_exceptions. At that time, I'll consider also the other issues logged by jeking3 (remove some Bjam warning, Boost.CI, Valgrind, etc.).

---

## Comment 2

> Username: lcaminiti  
> Created at: 2018-12-22 13:59:49 UTC  
> Url: https://github.com/boostorg/contract/issues/16#issuecomment-449572343  

I will fix this replacing std::uncaught_exception() with "boost::core::uncaught_exceptions() > 0" as suggested by pull request #19.

---

## Comment 3

> Username: lcaminiti  
> Created at: 2019-01-06 15:49:42 UTC  
> Url: https://github.com/boostorg/contract/issues/16#issuecomment-451751815  

fixed in develop branch (will merge in master for next boost release)
