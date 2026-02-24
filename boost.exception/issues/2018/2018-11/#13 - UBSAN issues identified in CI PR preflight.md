# #13 - UBSAN issues identified in CI PR preflight [Closed]

> Username: jeking3  
> Created at: 2018-11-12 21:39:59 UTC  
> Updated at: 2020-02-18 02:48:54 UTC  
> Closed at: 2020-02-18 02:48:54 UTC  
> Url: https://github.com/boostorg/exception/issues/13  

Following something seen during a CI PR preflight upgrade in Boost.Algorithm, I decided to apply the same CI PR to my fork of Boost.Exception, and we're seeing the same issues.  No analysis has been done on the observed failures.  
  
Build command:  
  
UBSAN_OPTIONS=print_stacktrace=1 /home/travis/build/jeking3/boost-root/b2 . toolset=gcc-8 cxxstd=03,11,14,17,2a cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined define=BOOST_NO_STRESS_TEST=1 linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug -j3  
  
First two errors:  
  
https://travis-ci.org/jeking3/exception/jobs/454168878#L1151  
```  
====== BEGIN OUTPUT ======  
../../boost/exception/exception.hpp:450:17: runtime error: member call on address 0x000000d37d60 which does not point to an object of type 'exception'  
0x000000d37d60: note: object has invalid vptr  
 ff 7f 00 00  00 00 00 00 00 00 00 00  08 3c 4a 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              invalid vptr  
    #0 0x4496be in boost::exception_detail::clone_impl<err>::~clone_impl() (/home/travis/build/jeking3/boost-root/bin.v2/libs/exception/test/copy_exception_test.test/gcc-8/debug/cxxstd-03-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden/copy_exception_test+0x4496be)  
    #1 0x7f0214a6d15e  (/usr/lib/x86_64-linux-gnu/libstdc++.so.6+0x6c15e)  
    #2 0x42981c in boost::exception_ptr boost::copy_exception<err>(err const&) (/home/travis/build/jeking3/boost-root/bin.v2/libs/exception/test/copy_exception_test.test/gcc-8/debug/cxxstd-03-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden/copy_exception_test+0x42981c)  
    #3 0x405eea in simple_test() ../../libs/exception/test/copy_exception_test.cpp:121  
    #4 0x4060fc in main ../../libs/exception/test/copy_exception_test.cpp:142  
    #5 0x7f0213503f44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #6 0x405768  (/home/travis/build/jeking3/boost-root/bin.v2/libs/exception/test/copy_exception_test.test/gcc-8/debug/cxxstd-03-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden/copy_exception_test+0x405768)  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
https://travis-ci.org/jeking3/exception/jobs/454168878#L1248  
```  
====== BEGIN OUTPUT ======  
../../boost/exception/exception.hpp:492:13: runtime error: member call on address 0x00000235c100 which does not point to an object of type 'exception'  
0x00000235c100: note: object has invalid vptr  
 fd 7f 00 00  00 00 00 00 00 00 00 00  28 24 43 00 00 00 00 00  40 c1 35 02 00 00 00 00  78 13 43 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              invalid vptr  
    #0 0x41d3c2 in boost::wrapexcept<test_exception>::~wrapexcept() ../../boost/exception/exception.hpp:492  
    #1 0x7fd7d9cd415e  (/usr/lib/x86_64-linux-gnu/libstdc++.so.6+0x6c15e)  
    #2 0x404671 in main ../../libs/exception/test/errinfos_test.cpp:49  
    #3 0x7fd7d8988f44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #4 0x4032c8  (/home/travis/build/jeking3/boost-root/bin.v2/libs/exception/test/errinfos_test.test/gcc-8/debug/cxxstd-03-iso/link-static/visibility-hidden/errinfos_test+0x4032c8)  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
For reference this started from a discussion here:  
  
https://github.com/boostorg/algorithm/issues/52

---

## Comment 1

> Username: zajo  
> Created at: 2019-03-21 19:30:41 UTC  
> Url: https://github.com/boostorg/exception/issues/13#issuecomment-475371529  

Here is the top function in the call stack (from `copy_exception_test.cpp`):  
  
```  
template <class T>  
inline  
exception_ptr  
copy_exception( T const & e )  
    {  
    try  
        {  
        throw enable_current_exception(e);  
        }  
    catch(  
    ... )  
        {  
        return current_exception();  
        }  
    }  
```  
  
The invocation:  
  
```  
boost::exception_ptr p = boost::copy_exception(err());  
```  
  
Where `err` derives from both `boost::exception` and `std::exception`, meaning `throw enable_current_exception(e)` throws `err`. And, according to the call stack, that results in UB.  
  
To help me diagnose this, maybe try if either of the following triggers this by itself:  
  
```  
throw err();  
```  
  
or  
  
```  
throw enable_current_exception(err());  
```

---

## Comment 2

> Username: zajo  
> Created at: 2020-02-18 02:48:54 UTC  
> Url: https://github.com/boostorg/exception/issues/13#issuecomment-587248203  

There is a UBSAN configuration in the current Boost Exception tests. If this is still an issue, is it possible to submit a PR which demonstrates the problem on Travis? Please feel free to reopen.
