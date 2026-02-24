# #158 Fix Intel compiler build [Merged]

> Username: iskunk  
> Created at: 2023-10-06 00:37:41 UTC  
> Updated at: 2023-10-06 01:21:16 UTC  
> Merged at: 2023-10-06 01:21:16 UTC  
> Closed at: 2023-10-06 01:21:16 UTC  
> Url: https://github.com/boostorg/core/pull/158  

In building the Boost tests with the Intel compiler on Linux/amd64, I encountered this link error:  
```  
[ 10%] Linking CXX executable ../../../stage/bin/boost_core-yield_prim_pthread_cancel_test  
icpc: warning #10182: disabling optimization; runtime debug checks enabled  
CMakeFiles/boost_core-yield_prim_pthread_cancel_test.dir/yield_prim_pthread_cancel_test.cpp.o: In function `boost::core::sp_thread_pause()':  
/tmp/boost/1.83.0/libs/core/include/boost/core/detail/sp_thread_pause.hpp:63: undefined reference to `__builtin_ia32_pause'  
make[3]: *** [libs/core/test/CMakeFiles/boost_core-yield_prim_pthread_cancel_test.dir/build.make:97: stage/bin/boost_core-yield_prim_pthread_cancel_test] Error 1  
make[2]: *** [CMakeFiles/Makefile2:12475: libs/core/test/CMakeFiles/boost_core-yield_prim_pthread_cancel_test.dir/all] Error 2  
make[1]: *** [CMakeFiles/Makefile2:7316: CMakeFiles/tests.dir/rule] Error 2  
make: *** [Makefile:427: tests] Error 2  
```  
  
I tracked this down to a typo: `__INTEL_COMPILER` should be prefixed by two underscores, not one. Every other instance of this symbol in the Boost source has two.

---
