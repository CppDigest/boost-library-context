# #170 - Test from boost libraries failed to run after boost submodule test updated from b56dc6(master) to f24f8f [Closed]

> Username: spacelg  
> Created at: 2018-10-08 08:15:58 UTC  
> Updated at: 2018-10-26 02:19:06 UTC  
> Closed at: 2018-10-26 02:19:06 UTC  
> Url: https://github.com/boostorg/test/issues/170  

Environment:  
 VS 2017 + Windows Server 2016  
  
Issue description:  
 We build and run test for boost. And we found one test failed to run after boost submodule test updated from b56dc6(master) to f24f8f [[(boostorg/boost@68a1e99)]](https://github.com/boostorg/boost/commit/68a1e99b5aaa26c238ea765fdce61b29794bcf09) . Details is as below. Could you please take a look?  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
5. .\b2 variant=release --build-dir=..\out\x86rel address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\test\test  
  
[log_x86_test_104.log](https://github.com/boostorg/test/files/2455107/log_x86_test_104.log)  
  
ErrorMessage:  
====== BEGIN OUTPUT ======  
Running 1 test case...  
  
*** 1 failure is detected in the test suite "fake master"  
libs/test/test/execution_monitor-ts/boost_exception-test.cpp(63): info: check error_string.find("tag_error_code") != std::string::npos has passed  
libs/test/test/execution_monitor-ts/boost_exception-test.cpp(64): info: check error_string.find("= 123") != std::string::npos has passed  
libs/test/test/execution_monitor-ts/boost_exception-test.cpp(65): info: check error_string.find("Dynamic exception type") != std::string::npos has passed  
libs/test/test/execution_monitor-ts/boost_exception-test.cpp(66): error: in "test_logs": check error_string.find("boost::wrapexcept<") != std::string::npos has failed [4294967295 == 4294967295]  
libs/test/test/execution_monitor-ts/boost_exception-test.cpp(67): info: check error_string.find("myexception>") != std::string::npos has passed  
libs\test\test\execution_monitor-ts\boost_exception-test.cpp(43): Leaving test case "test_logs"; testing time: 2ms  
Leaving test module "Master Test Suite"; testing time: 2ms  
  
*** 1 failure is detected in the test module "Master Test Suite"  
   
EXIT STATUS: 201   
====== END OUTPUT ======

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-10-08 17:43:12 UTC  
> Url: https://github.com/boostorg/test/issues/170#issuecomment-427920908  

First of all, thank you for your report!  
  
This test checks that the Boost.Test framework is able to properly catch exceptions of the  `boost::exception` kind. To perform the check, I analyse the returned string. Unfortunately, this string is different on the platforms/compilers and has recently changed in the `boost.exception` library.  
  
This is rather a test issue than a library issue, you can ignore it and I will update asap.

---

## Comment 2

> Username: spacelg  
> Created at: 2018-10-26 02:19:06 UTC  
> Url: https://github.com/boostorg/test/issues/170#issuecomment-433264297  

Thanks for your feedback. we verified this issue has been fixed. Close it.
