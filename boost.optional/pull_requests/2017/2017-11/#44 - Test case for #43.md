# #44 Test case for #43 [Merged]

> Username: vinniefalco  
> Created at: 2017-11-04 03:46:03 UTC  
> Updated at: 2017-11-04 16:46:49 UTC  
> Merged at: 2017-11-04 16:46:49 UTC  
> Closed at: 2017-11-04 16:46:49 UTC  
> Url: https://github.com/boostorg/optional/pull/44  

Output:  
```  
vinnie@corsair-ubuntu:~/src/boost/libs/optional$ b2 -j6 test toolset=gcc cxxstd=11  
Performing configuration checks  
  
    - 32-bit                   : no  (cached)  
    - 64-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...found 1377 targets...  
...updating 6 targets...  
  
gcc.compile.c++ ../../bin.v2/libs/optional/test/optional_test_tc_base.test/gcc-gnu-5.4.0/debug/cxxstd-11/threadapi-pthread/optional_test_tc_base.o  
test/optional_test_tc_base.cpp: In instantiation of ‘W<T>::W(Args&& ...) [with Args = {}; T = S&]’:  
/usr/include/c++/5/type_traits:1323:12:   required from ‘struct std::is_trivially_constructible<W<S&> >’  
/usr/include/c++/5/type_traits:1330:12:   required from ‘struct std::is_trivially_default_constructible<W<S&> >’  
../../boost/optional/optional.hpp:844:80:   required from ‘struct boost::optional_config::is_type_trivial<W<S&> >’  
../../boost/optional/optional.hpp:869:7:   required from ‘class boost::optional<W<S&> >’  
test/optional_test_tc_base.cpp:65:26:   required from here  
test/optional_test_tc_base.cpp:54:35: error: value-initialization of reference type ‘S&’  
   : t_(std::forward<Args>(args)...)  
                                   ^  
```

---
