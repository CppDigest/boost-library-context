# #190 fix warning on gcc-7.3 in cygwin claiming master_test_suite is declared differently [Closed]

> Username: jeking3  
> Created at: 2018-11-23 22:00:45 UTC  
> Updated at: 2019-02-08 17:24:49 UTC  
> Closed at: 2019-02-02 12:51:54 UTC  
> Url: https://github.com/boostorg/test/pull/190  

See an example here:  
https://ci.appveyor.com/project/jeking3/thread/builds/20518379/job/m0ppxtti4invy0ka?fullLog=true#L1459  
  
```  
gcc.compile.c++ bin.v2\libs\thread\test\test_thread_exit.test\gcc-7.3.0\rls\cxstd-03-iso\trgt-os-cygwn\thrdp-pthrd\thrd-mlt\vsblt-hdn\test_thread_exit.o  
In file included from ./boost/test/tree/auto_registration.hpp:21:0,  
                 from ./boost/test/unit_test_suite.hpp:17,  
                 from ./boost/test/unit_test.hpp:19,  
                 from libs\thread\test\test_thread_exit.cpp:17:  
./boost/test/tree/test_unit.hpp:232:33: warning: 'boost::unit_test::master_test_suite_t& boost::unit_test::framework::master_test_suite()' redeclared without dllimport attribute: previous dllimport ignored [-Wattributes]  
     friend master_test_suite_t& boost::unit_test::framework::master_test_suite();  
                                 ^~~~~  
```

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2019-02-02 12:51:54 UTC  
> Url: https://github.com/boostorg/test/pull/190#issuecomment-459962840  

Fixed via acbcd749933e7d398a7bf79b087de802efd961c5 and 775114eb5b9baac93e4e72fefdb2e528ac995e82 Thanks

---
