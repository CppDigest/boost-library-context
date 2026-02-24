# #15 - Build failure with gcc 4.6 (57 issues) [Closed]

> Username: jeking3  
> Created at: 2018-11-04 22:22:40 UTC  
> Updated at: 2019-06-02 16:36:24 UTC  
> Closed at: 2019-06-02 16:36:24 UTC  
> Url: https://github.com/boostorg/contract/issues/15  

https://api.travis-ci.org/v3/job/450610130/log.txt  
Here's an example of one:  
```  
gcc.compile.c++ ../../bin.v2/libs/contract/test/old-if_copyable.test/gcc-4.6/debug/cxxstd-0x-iso/visibility-hidden/old/if_copyable.o  
In file included from ../../libs/contract/test/old/if_copyable.cpp:14:0:  
../../libs/contract/test/old/if_copyable.hpp: In constructor â€˜boost::contract::old_value_copy<T>::old_value_copy(const T&) [with T = ncp]â€™:  
../../boost/contract/old.hpp:505:55:   instantiated from â€˜boost::contract::old_value::old_value(const T&, typename boost::enable_if<boost::contract::is_old_value_copyable<T> >::type*) [with T = ncp, typename boost::enable_if<boost::contract::is_old_value_copyable<T> >::type = void]â€™  
../../libs/contract/test/old/if_copyable.cpp:67:53:   instantiated from â€˜void next(T&) [with T = ncp]â€™  
../../libs/contract/test/old/if_copyable.cpp:103:11:   instantiated from here  
../../libs/contract/test/old/if_copyable.hpp:41:5: error: â€˜ncp::ncp(const ncp&)â€™ is private  
../../boost/contract/old.hpp:213:21: error: within this context  
  
    "g++-4.6"   -std=c++0x -fvisibility-inlines-hidden -fPIC -m64 -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTRACT_DISABLE_THREADS -DBOOST_CONTRACT_DYN_LINK -DBOOST_SYSTEM_DYN_LINK=1  -I"../.." -I"../../libs/contract/test/old" -c -o "../../bin.v2/libs/contract/test/old-if_copyable.test/gcc-4.6/debug/cxxstd-0x-iso/visibility-hidden/old/if_copyable.o" "../../libs/contract/test/old/if_copyable.cpp"  
  
...failed gcc.compile.c++ ../../bin.v2/libs/contract/test/old-if_copyable.test/gcc-4.6/debug/cxxstd-0x-iso/visibility-hidden/old/if_copyable.o...  
```

---

## Comment 1

> Username: lcaminiti  
> Created at: 2018-12-22 14:14:14 UTC  
> Updated at: 2018-12-22 14:23:34 UTC  
> Url: https://github.com/boostorg/contract/issues/15#issuecomment-449573422  

This is a known test failure on GCC 4.6 due to the fact that compiler version incorrectly implements SFINAE. It is documented here:  
https://www.boost.org/development/tests/master/developer/output/igaztanaga-master-gcc-4-6c++11-boost-bin-v2-libs-contract-test-old-if_copyable-test-gcc-4-6c+-dbg-dbg-symbl-off-vsblt-hdn.html  
I'll double check this and close it accordingly.

---

## Comment 2

> Username: jeking3  
> Created at: 2019-01-06 15:57:54 UTC  
> Url: https://github.com/boostorg/contract/issues/15#issuecomment-451752476  

Probably worth documenting the oldest known compilers that work and are regularly tested against, if not already done in the docs.

---

## Comment 3

> Username: lcaminiti  
> Created at: 2019-01-11 16:07:38 UTC  
> Updated at: 2019-01-11 16:09:16 UTC  
> Url: https://github.com/boostorg/contract/issues/15#issuecomment-453567367  

Yes, I am in the process of cleaning up a few warnings and errors that show on the regression test table. I see this is one such errors and I will mark it as a known failures on the table that way it will be documented (the docs already point to the regression test table).

---

## Comment 4

> Username: lcaminiti  
> Created at: 2019-06-02 16:36:24 UTC  
> Url: https://github.com/boostorg/contract/issues/15#issuecomment-498046278  

Marked some of these as expected failures with relative comment, also cleaned up some warnings.
