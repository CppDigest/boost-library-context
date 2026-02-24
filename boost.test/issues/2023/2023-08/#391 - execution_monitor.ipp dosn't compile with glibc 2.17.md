# #391 - execution_monitor.ipp dosn't compile with glibc 2.17 [Closed]

> Username: ChristophStrehle  
> Created at: 2023-08-03 09:42:17 UTC  
> Updated at: 2023-08-04 13:14:39 UTC  
> Closed at: 2023-08-04 13:14:39 UTC  
> Url: https://github.com/boostorg/test/issues/391  

With glibc 2.17 there is a compile break in execution_monitor.ipp since [this commit](https://github.com/boostorg/test/commit/884646245fe5e4efb0d23a0f5e405110faed4684) because the macro  PRIxPTR is not defined. The macro is only defined in inttypes.h when defining __STDC_FORMAT_MACROS before the include.   
  
compile break:  
`In file included from libs/test/src/execution_monitor.cpp:16:  
./boost/test/impl/execution_monitor.ipp: In member function 'void boost::detail::system_signal_exception::report() const':  
./boost/test/impl/execution_monitor.ipp:435:88: error: expected ')' before 'PRIxPTR'  
  435 |                           "signal: illegal opcode; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
      |                                                                                        ^  
      |                                                                                        )`  
  
Unfortunately we still use this old version of glibc in our toolchain because we still support this version on some IoT devices.

---

## Comment 1

> Username: mborland  
> Created at: 2023-08-03 14:11:24 UTC  
> Url: https://github.com/boostorg/test/issues/391#issuecomment-1664059932  

Looks like `__STDC_FORMAT_MACROS` was required from Glibc 2.2 to 2.17. I'll have a fix up shortly.
