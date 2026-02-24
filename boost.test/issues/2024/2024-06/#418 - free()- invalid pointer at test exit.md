# #418 - free(): invalid pointer at test exit [Closed]

> Username: arkadesOrg  
> Created at: 2024-06-09 11:01:29 UTC  
> Updated at: 2024-06-25 12:51:18 UTC  
> Closed at: 2024-06-18 13:28:47 UTC  
> Url: https://github.com/boostorg/test/issues/418  

Can someone please give me a hint where the cause of this error may be located?  
I am using Boost 1.83.0  
  
when I run my test manually I get:  
```console  
Running 1 test case...  
  
*** No errors detected  
free(): invalid pointer  
Aborted (core dumped)  
```  
  
here is the test source:  
```c++  
#define BOOST_TEST_MODULE MyTest  
#include <boost/test/included/unit_test.hpp>  
    
BOOST_AUTO_TEST_CASE( free_test_function )  
/* Compare with void free_test_function() */  
{  
  BOOST_TEST( true /* test assertion */ );  
}  
```  
  
The relevant valgrind memory analysis I got:  
```valgrind  
==39340== Invalid free() / delete / delete[] / realloc()  
==39340==    at 0x48468BD: operator delete(void*, unsigned long) (vg_replace_malloc.c:1181)  
==39340==    by 0x4D24A5F: __cxa_finalize (cxa_finalize.c:82)  
==39340==    by 0x48D72F7: ??? (in /usr/lib/libboost_unit_test_framework.so.1.83.0)  
==39340==    by 0x40010F1: _dl_call_fini (dl-call_fini.c:43)  
==39340==    by 0x400511D: _dl_fini (dl-fini.c:114)  
==39340==    by 0x4D24FA0: __run_exit_handlers (exit.c:108)  
==39340==    by 0x4D2506D: exit (exit.c:138)  
==39340==    by 0x4D0BC8E: (below main) (libc_start_call_main.h:74)  
==39340==  Address 0x4eea7c0 is 0 bytes inside a block of size 18 free'd  
==39340==    at 0x4846404: operator delete(void*) (vg_replace_malloc.c:1131)  
==39340==    by 0x4D24FA0: __run_exit_handlers (exit.c:108)  
==39340==    by 0x4D2506D: exit (exit.c:138)  
==39340==    by 0x4D0BC8E: (below main) (libc_start_call_main.h:74)  
==39340==  Block was alloc'd at  
==39340==    at 0x4842F73: operator new(unsigned long) (vg_replace_malloc.c:487)  
==39340==    by 0x180DFD: void std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::_M_construct<char const*>(char const*, char const*, std::forward_iterator_tag) (basic_string.tcc:229)  
==39340==    by 0x16D951: std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::basic_string<std::allocator<char> >(char const*, std::allocator<char> const&) (basic_string.h:654)  
==39340==    by 0x154857: __static_initialization_and_destruction_0() (unit_test_parameters.ipp:97)  
==39340==    by 0x155075: _GLOBAL__sub_I__ZN5boost9unit_test6output22compiler_log_formatter9log_startERSom (testcase_simple.cpp:21)  
==39340==    by 0x4D0BDC3: call_init (libc-start.c:145)  
==39340==    by 0x4D0BDC3: __libc_start_main@@GLIBC_2.34 (libc-start.c:347)  
==39340==    by 0x121C04: (below main) (in /home/domson/debug/build/src/tests/testcase_simple)  
```  
  
I build with cmake:  
  
```cmake  
# Testing Framework  
find_package(Boost REQUIRED COMPONENTS unit_test_framework)  
  
add_executable(testcase_simple testcase_simple.cpp)  
target_link_libraries(testcase_simple Boost::unit_test_framework)  
  
enable_testing()  
  
add_test(NAME TestcaseSimple COMMAND testcase_simple)   
```  
  
Btw did I mention I use Arch? :)  
  
Thank you!

---

## Comment 1

> Username: arkadesOrg  
> Created at: 2024-06-18 13:28:47 UTC  
> Url: https://github.com/boostorg/test/issues/418#issuecomment-2176106261  

With cmake I had to add the definition to use shared libraries for my specific use case:  
  
`set_target_properties(my_test PROPERTIES COMPILE_DEFINITIONS "BOOST_TEST_DYN_LINK")`  
  
Thanks for this awesome Boost framework!

---

## Comment 2

> Username: tranqui  
> Created at: 2024-06-23 16:20:54 UTC  
> Url: https://github.com/boostorg/test/issues/418#issuecomment-2185108318  

I get the same error, also on arch with boost 1.83.0. However, setting BOOST_TEST_DYN_LINK did not fix it in my case.

---

## Comment 3

> Username: arkadesOrg  
> Created at: 2024-06-24 08:10:40 UTC  
> Url: https://github.com/boostorg/test/issues/418#issuecomment-2185878999  

Did you also try to set up a very basic test case?

---

## Comment 4

> Username: tranqui  
> Created at: 2024-06-24 11:09:21 UTC  
> Updated at: 2024-06-24 11:15:05 UTC  
> Url: https://github.com/boostorg/test/issues/418#issuecomment-2186310132  

Yes, mine was essentially identical to yours just with different filenames.  
  
I also tried direct compilation with `g++ -o unit_test unit_test.cc -lboost_unit_test_framework` with the same memory leak. Here `unit_test.cc` could be your code snippet to reproduce the error.

---

## Comment 5

> Username: arkadesOrg  
> Created at: 2024-06-24 19:17:11 UTC  
> Updated at: 2024-06-24 19:18:23 UTC  
> Url: https://github.com/boostorg/test/issues/418#issuecomment-2187242228  

I see, so you generelly link with cmake, but also tried to directly do it. Should'nt you have passed the `-DBOOST_TEST_DYN_LINK` defintion to g++?  
  
PS: do you have a similar valgrind output?

---

## Comment 6

> Username: tranqui  
> Created at: 2024-06-24 20:55:59 UTC  
> Url: https://github.com/boostorg/test/issues/418#issuecomment-2187387734  

Ah, yeah I just manually placed the #define BOOST_TEST_DYN_LINK inside the cpp file for the manual compilation.  
  
Yes re: similar valgrind output:  
  
```  
==90488== Invalid free() / delete / delete[] / realloc()  
==90488==    at 0x48468BD: operator delete(void*, unsigned long) (vg_replace_malloc.c:1181)  
==90488==    by 0x4D0BA5F: __cxa_finalize (cxa_finalize.c:82)  
==90488==    by 0x48BE2F7: ??? (in /usr/lib/libboost_unit_test_framework.so.1.83.0)  
==90488==    by 0x40010F1: _dl_call_fini (dl-call_fini.c:43)  
==90488==    by 0x400511D: _dl_fini (dl-fini.c:114)  
==90488==    by 0x4D0BFA0: __run_exit_handlers (exit.c:108)  
==90488==    by 0x4D0C06D: exit (exit.c:138)  
==90488==    by 0x4CF2C8E: (below main) (libc_start_call_main.h:74)  
==90488==  Address 0x4ed17c0 is 0 bytes inside a block of size 18 free'd  
==90488==    at 0x4846404: operator delete(void*) (vg_replace_malloc.c:1131)  
==90488==    by 0x4D0BFA0: __run_exit_handlers (exit.c:108)  
==90488==    by 0x4D0C06D: exit (exit.c:138)  
==90488==    by 0x4CF2C8E: (below main) (libc_start_call_main.h:74)  
==90488==  Block was alloc'd at  
==90488==    at 0x4842F73: operator new(unsigned long) (vg_replace_malloc.c:487)  
==90488==    by 0x180DA5: void std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::_M_construct<char const*>(char const*, char const*, std::forward_iterator_tag) (in /home/joshua/unit_test)  
==90488==    by 0x16D8F9: std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::basic_string<std::allocator<char> >(char const*, std::allocator<char> const&) (in /home/joshua/unit_test)  
==90488==    by 0x1547FF: __static_initialization_and_destruction_0() (in /home/joshua/unit_test)  
==90488==    by 0x15501D: _GLOBAL__sub_I__ZN5boost9unit_test6output22compiler_log_formatter9log_startERSom (in /home/joshua/unit_test)  
==90488==    by 0x4CF2DC3: call_init (libc-start.c:145)  
==90488==    by 0x4CF2DC3: __libc_start_main@@GLIBC_2.34 (libc-start.c:347)  
==90488==    by 0x121C04: (below main) (in /home/joshua/unit_test)  
```  
  
And compiling with -fsanitize=leak:  
```  
==90960==ERROR: LeakSanitizer: detected memory leaks  
  
Direct leak of 23 byte(s) in 1 object(s) allocated from:  
    #0 0x78944c0fd682 in operator new(unsigned long) /usr/src/debug/gcc/gcc/libsanitizer/asan/asan_new_delete.cpp:95  
    #1 0x78944bf9269e  (/usr/lib/libboost_unit_test_framework.so.1.83.0+0x2769e) (BuildId: c157e439137327cbc19d9c2cfa9936099dbf1f09)  
    #2 0x78944bf8df12  (/usr/lib/libboost_unit_test_framework.so.1.83.0+0x22f12) (BuildId: c157e439137327cbc19d9c2cfa9936099dbf1f09)  
    #3 0x78944c72d2e6  (/lib64/ld-linux-x86-64.so.2+0x52e6) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
    #4 0x78944c72d3dc  (/lib64/ld-linux-x86-64.so.2+0x53dc) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
    #5 0x78944c74479f  (/lib64/ld-linux-x86-64.so.2+0x1c79f) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
  
Direct leak of 21 byte(s) in 1 object(s) allocated from:  
    #0 0x78944c0fd682 in operator new(unsigned long) /usr/src/debug/gcc/gcc/libsanitizer/asan/asan_new_delete.cpp:95  
    #1 0x78944bf9269e  (/usr/lib/libboost_unit_test_framework.so.1.83.0+0x2769e) (BuildId: c157e439137327cbc19d9c2cfa9936099dbf1f09)  
    #2 0x78944bf8dd32  (/usr/lib/libboost_unit_test_framework.so.1.83.0+0x22d32) (BuildId: c157e439137327cbc19d9c2cfa9936099dbf1f09)  
    #3 0x78944c72d2e6  (/lib64/ld-linux-x86-64.so.2+0x52e6) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
    #4 0x78944c72d3dc  (/lib64/ld-linux-x86-64.so.2+0x53dc) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
    #5 0x78944c74479f  (/lib64/ld-linux-x86-64.so.2+0x1c79f) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
  
Direct leak of 20 byte(s) in 1 object(s) allocated from:  
    #0 0x78944c0fd682 in operator new(unsigned long) /usr/src/debug/gcc/gcc/libsanitizer/asan/asan_new_delete.cpp:95  
    #1 0x78944bf9269e  (/usr/lib/libboost_unit_test_framework.so.1.83.0+0x2769e) (BuildId: c157e439137327cbc19d9c2cfa9936099dbf1f09)  
    #2 0x78944bf8dd5a  (/usr/lib/libboost_unit_test_framework.so.1.83.0+0x22d5a) (BuildId: c157e439137327cbc19d9c2cfa9936099dbf1f09)  
    #3 0x78944c72d2e6  (/lib64/ld-linux-x86-64.so.2+0x52e6) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
    #4 0x78944c72d3dc  (/lib64/ld-linux-x86-64.so.2+0x53dc) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
    #5 0x78944c74479f  (/lib64/ld-linux-x86-64.so.2+0x1c79f) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
  
Direct leak of 20 byte(s) in 1 object(s) allocated from:  
    #0 0x78944c0fd682 in operator new(unsigned long) /usr/src/debug/gcc/gcc/libsanitizer/asan/asan_new_delete.cpp:95  
    #1 0x78944bf9269e  (/usr/lib/libboost_unit_test_framework.so.1.83.0+0x2769e) (BuildId: c157e439137327cbc19d9c2cfa9936099dbf1f09)  
    #2 0x78944bf8dce2  (/usr/lib/libboost_unit_test_framework.so.1.83.0+0x22ce2) (BuildId: c157e439137327cbc19d9c2cfa9936099dbf1f09)  
    #3 0x78944c72d2e6  (/lib64/ld-linux-x86-64.so.2+0x52e6) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
    #4 0x78944c72d3dc  (/lib64/ld-linux-x86-64.so.2+0x53dc) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
    #5 0x78944c74479f  (/lib64/ld-linux-x86-64.so.2+0x1c79f) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
  
Direct leak of 18 byte(s) in 1 object(s) allocated from:  
    #0 0x78944c0fd682 in operator new(unsigned long) /usr/src/debug/gcc/gcc/libsanitizer/asan/asan_new_delete.cpp:95  
    #1 0x78944bf9269e  (/usr/lib/libboost_unit_test_framework.so.1.83.0+0x2769e) (BuildId: c157e439137327cbc19d9c2cfa9936099dbf1f09)  
    #2 0x78944bf8e02a  (/usr/lib/libboost_unit_test_framework.so.1.83.0+0x2302a) (BuildId: c157e439137327cbc19d9c2cfa9936099dbf1f09)  
    #3 0x78944c72d2e6  (/lib64/ld-linux-x86-64.so.2+0x52e6) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
    #4 0x78944c72d3dc  (/lib64/ld-linux-x86-64.so.2+0x53dc) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
    #5 0x78944c74479f  (/lib64/ld-linux-x86-64.so.2+0x1c79f) (BuildId: c560bca2bb17f5f25c6dafd8fc19cf1883f88558)  
  
SUMMARY: AddressSanitizer: 102 byte(s) leaked in 5 allocation(s).  
```  
  
I suspect this is an arch linux problem rather than a boost problem.  
  
I also tried to statically link the boost libraries, but got multiple definition linker errors.

---

## Comment 7

> Username: arkadesOrg  
> Created at: 2024-06-24 22:41:20 UTC  
> Url: https://github.com/boostorg/test/issues/418#issuecomment-2187542544  

`#define BOOST_TEST_DYN_LINK` is exactly what I tried also, but `-DBOOST_TEST_DYN_LINK` did the trick for some reason. I build on arch linux, and the testcases now exit properly.

---

## Comment 8

> Username: tranqui  
> Created at: 2024-06-25 12:50:52 UTC  
> Updated at: 2024-06-25 12:51:18 UTC  
> Url: https://github.com/boostorg/test/issues/418#issuecomment-2188853566  

I have tried both to no avail.
