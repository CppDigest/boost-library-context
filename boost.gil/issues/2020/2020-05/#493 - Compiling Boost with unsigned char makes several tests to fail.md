# #493 - Compiling Boost with unsigned char makes several tests to fail [Closed]

> Username: trex58  
> Created at: 2020-05-11 13:21:11 UTC  
> Updated at: 2020-05-11 21:26:59 UTC  
> Closed at: 2020-05-11 21:26:59 UTC  
> Url: https://github.com/boostorg/gil/issues/493  

### Actual behavior  
  
I've been unable to find any information about Boost being able to manage both signed char and unsigned char.  
My expectation is that Boost is able to manage both cases.  
  
On Fedora/x86_64, C/C++ char type is signed ( -127 to +127 ) by default.  
On other operating systems, like AIX, char is unsigned (0 .. 256) by default.  
  
On Fedora/x86_64, I've first compiled/tested Boost with no option, thus using (default) signed char.  
Then, I've rebuilt/retested Boost with **-funsigned-char**, and I've found that several tests are now failing (see below for the complete list).  
  
Here below, C means : failure at compilation time, and T means : failure at testing time .  
```  
   - math :   
       C test_polynomial.o             ../libs/math/test/test_polynomial.cpp:394:24: error: narrowing conversion of ‘-5’ from ‘int’ to ‘char’ [-Wnarrowing]  394 |     boost::array<T, 4> tmp = {{8, -5, -4, 3}};  
   - mpl :  
       C char.o...                      error: narrowing conversion of ‘-1’ from ‘int’ to ‘char’ [-Wnarrowing]  
       C integral_c.o                   error: narrowing conversion of ‘-1’ from ‘int’ to ‘char’ [-Wnarrowing]  
   - config :  
       T config_test_no_except.run...   Failed to value-initialize m_2d_char_array.  
       T config_test_no_rtti.run...     Failed to value-initialize m_2d_char_array.  
       T config_test.run...             Failed to value-initialize m_2d_char_array.  
       T config_test_threaded.run...    Failed to value-initialize m_2d_char_array.  
   - lambda :  
       T result_of_tests.run...         ../libs/lambda/test/result_of_tests.cpp(303): test apply1<int>(_1, one) == 1 failed  
   - lockfree :  
       T freelist_test.run...           freelist_test: ../libs/lockfree/test/freelist_test.cpp:37: dummy::dummy(): Assertion `allocated == 0' failed.  
   - process :  
       T async_pipe.run...              terminate called after throwing an instance of 'boost::process::process_errorterminate called recursively  
   - stacktrace :  
       T noop_terminate_handler_no_dbg.run...  terminate called after throwing an instance of 'boost::interprocess::interprocess_exception'  
       T noop_terminate_handler.run...         terminate called after throwing an instance of 'boost::interprocess::interprocess_exception'  
   - wave :  
       T testwave_dll.run...            (1 test(s) failed)  
       T testwave.run...                (1 test(s) failed)  
```  
  
About the **math:test_polynomial test**, it is easy to use casting like: static_cast<T>(-5) so that errors disappear. Then, the test works perfectly well on Fedora/x86_64. However, on AIX (unsigned char by default), a conversion from double to unsigned char breaks negative value (replacing them by: 0) due to the use of the Power "fctiwuz f0,f0" assembly instruction, and thus the test fails.  
  
### Expected  behavior  
  
All the above tests do succeed when not using unsigned char (-funsigned-char).  
  
### C++ Minimal Working Example  
  
See the above Boost tests.  
  
### Environment  
  
- OperatingSystem/Architecture: Fedora31 / x86_64  
- Compiler version: GCC 9.3.1 - 20200408  
- Build settings:  
- Version (Git ref or `<boost/version.hpp>`): 1.73.0

---

## Comment 1

> Username: trex58  
> Created at: 2020-05-11 13:22:39 UTC  
> Url: https://github.com/boostorg/gil/issues/493#issuecomment-626698658  

Nuts. I opened this defect on the gil library by mistake. Sorry. Is it possible to move it to "general" boost?

---

## Comment 2

> Username: mloskot  
> Created at: 2020-05-11 17:11:28 UTC  
> Url: https://github.com/boostorg/gil/issues/493#issuecomment-626832742  

@trex58  I don't see any option to move this issue to any other repo. You'll have to re-submit it.

---

## Comment 3

> Username: trex58  
> Created at: 2020-05-11 21:26:59 UTC  
> Url: https://github.com/boostorg/gil/issues/493#issuecomment-626974505  

OK. Thx. Re-opened.
