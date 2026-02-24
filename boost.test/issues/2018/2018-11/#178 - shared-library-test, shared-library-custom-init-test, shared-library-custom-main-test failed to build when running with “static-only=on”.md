# #178 - shared-library-test, shared-library-custom-init-test, shared-library-custom-main-test failed to build when running with “static-only=on” [Closed]

> Username: BinCaoWR  
> Created at: 2018-11-09 08:11:42 UTC  
> Updated at: 2020-05-09 09:52:16 UTC  
> Closed at: 2020-05-09 09:52:16 UTC  
> Url: https://github.com/boostorg/test/issues/178  

Build log:  
clang-vxworks.link ../bin.v2/libs/test/test/shared-library-test.test/clang-vxworks/debug/cross-compile-vxworks/static-only-on/shared-library-test.vxe  
../crt0.o: In function `_start':  
crtrtp.c:(.text+0x58): undefined reference to `main'  
../bin.v2/libs/test/test/shared-library-test.test/clang-vxworks/debug/cross-compile-vxworks/static-only-on/usage-variants-ts/shared-library-test.o: In function `init_unit_test_suite(int, char**)':  
../boost/test/unit_test_suite.hpp:383: undefined reference to `boost::unit_test::framework::master_test_suite()'  
../bin.v2/libs/test/test/shared-library-test.test/clang-vxworks/debug/cross-compile-vxworks/static-only-on/usage-variants-ts/shared-library-test.o: In function `test_invoker()':  
../libs/test/test/usage-variants-ts/shared-library-test.cpp:19: undefined reference to `boost::unit_test::unit_test_log_t::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned int, boost::unit_test::basic_cstring<char const>)'  
  
clang-vxworks.compile.c++ ../bin.v2/libs/test/test/shared-library-custom-init-test.test/clang-vxworks/debug/cross-compile-vxworks/static-only-on/usage-variants-ts/shared-library-custom-init-test.o  
../libs/test/test/usage-variants-ts/shared-library-custom-init-test.cpp:36:44: error: cannot initialize a parameter of type 'boost::unit_test::init_unit_test_func' (aka 'boost::unit_test::test_suite *(*)(int, char **)') with an rvalue of type 'bool (*)()': different number of parameters (2 vs 0)  
  return boost::unit_test::unit_test_main( &init_unit_test, argc, argv );  
                                           ^~~~~~~~~~~~~~~  
  
clang-vxworks.compile.c++ ../bin.v2/libs/test/test/shared-library-custom-main-test.test/clang-vxworks/debug/cross-compile-vxworks/static-only-on/usage-variants-ts/shared-library-custom-main-test.o  
../libs/test/test/usage-variants-ts/shared-library-custom-main-test.cpp:30:45: error: use of undeclared identifier 'init_unit_test'  
  return boost::unit_test::unit_test_main( &init_unit_test, argc, argv );  
  
Potential solution:  
Update libs/test/test/Jamfile.v2 the following lines, to add ": : : : : <link>shared" at the end of the lines:  
[ boost.test-self-test run : usage-variants-ts : shared-library-test : boost_unit_test_framework/<link>shared : : : : : <link>shared ]  
[ boost.test-self-test run : usage-variants-ts : shared-library-custom-init-test : boost_unit_test_framework/<link>shared : : : : : <link>shared ]  
[ boost.test-self-test run : usage-variants-ts : shared-library-custom-main-test : boost_unit_test_framework/<link>shared : : : : : <link>shared ]  
  
So when boost is running with “static-only=on”, these tests can be disabled by adding "<static-only>on,<link>shared:<build>no" in our project requirements.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-04-18 17:44:43 UTC  
> Url: https://github.com/boostorg/test/issues/178#issuecomment-615910657  

I am trying to reproduce this but I do not have any option such as `static-only=on`. Would you please describe what/how to use this option?
