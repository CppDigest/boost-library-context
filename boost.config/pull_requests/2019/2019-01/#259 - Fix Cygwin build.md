# #259 Fix Cygwin build. [Closed]

> Username: egorpugin  
> Created at: 2019-01-20 21:18:45 UTC  
> Updated at: 2019-01-20 21:39:51 UTC  
> Closed at: 2019-01-20 21:39:51 UTC  
> Url: https://github.com/boostorg/config/pull/259  

Without this I see the following error (cygwin, gcc-8.2.0, boost 1.69.0):  
```  
In file included from d:/dev/cppan2_storage/pkg/fb/cc/14/03/9dd1/src/sdir/include/boost/thread/detail/platform.hpp:17,  
                 from d:/dev/cppan2_storage/pkg/fb/cc/14/03/9dd1/src/sdir/include/boost/thread/detail/config.hpp:13,  
                 from d:/dev/cppan2_storage/pkg/fb/cc/14/03/9dd1/src/sdir/src/pthread/once.cpp:6:  
d:/dev/cppan2_storage/pkg/a3/34/a0/78/8681/src/sdir/include/boost/config/requires_threads.hpp:29:4: error: #error "Threading support unavaliable: it has been explicitly disabled with BOOST_DISABLE_THREADS"  
 #  error "Threading support unavaliable: it has been explicitly disabled with BOOST_DISABLE_THREADS"  
    ^~~~~  
```  
  
ps:  
It is possible that for some reason I've got bad gcc/libstdc++ build, so explicit statement is needed.  
  
pps:   
Yes, seems bad gcc config. Sorry.

---
