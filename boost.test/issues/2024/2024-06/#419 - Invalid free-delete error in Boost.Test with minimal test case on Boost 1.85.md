# #419 - Invalid free/delete error in Boost.Test with minimal test case on Boost 1.85 [Open]

> Username: bullvolkan  
> Created at: 2024-06-27 09:51:13 UTC  
> Updated at: 2024-06-27 12:52:04 UTC  
> Url: https://github.com/boostorg/test/issues/419  

I encountered an invalid free/delete error when running a minimal test case using Boost.Test with Boost 1.85. Below is the code and the error message from Valgrind.  
  
Minimal Test Case:  
  
```  
#define BOOST_TEST_MODULE MinimalTest  
#include <boost/test/included/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE(minimal_test)  
{  
    BOOST_CHECK_EQUAL(1, 1);  
}  
```  
  
Error Message from Valgrind:  
  
>   
> ==49727== Invalid free() / delete / delete[] / realloc()  
> ==49727==    at 0x484668F: operator delete(void*) (vg_replace_malloc.c:1131)  
> ==49727==    by 0x4CD467F: __cxa_finalize (cxa_finalize.c:82)  
> ==49727==    by 0x487CD96: ??? (in /usr/local/lib/libboost_unit_test_framework.so.1.85.0)  
> ==49727==    by 0x40010F1: _dl_call_fini (dl-call_fini.c:43)  
> ==49727==    by 0x40050AD: _dl_fini (dl-fini.c:114)  
> ==49727==    by 0x4CD4BB0: __run_exit_handlers (exit.c:108)  
> ==49727==    by 0x4CD4C7D: exit (exit.c:138)  
> ==49727==    by 0x4CBC08E: (below main) (libc_start_call_main.h:74)  
> ==49727==  Address 0x4e957c0 is 0 bytes inside a block of size 18 free'd  
> ==49727==    at 0x4846B81: operator delete(void*, unsigned long) (vg_replace_malloc.c:1181)  
> ==49727==    by 0x487715: std::__new_allocator<char>::deallocate(char*, unsigned long) (new_allocator.h:172)  
> ==49727==    by 0x45D40E: UnknownInlinedFun (allocator.h:208)  
> ==49727==    by 0x45D40E: UnknownInlinedFun (alloc_traits.h:513)  
> ==49727==    by 0x45D40E: std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::_M_destroy(unsigned long) (basic_string.h:294)  
> ==49727==    by 0x45BA33: std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::_M_dispose() (basic_string.h:288)  
> ==49727==    by 0x415A25: std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::~basic_string() (basic_string.h:809)  
> ==49727==    by 0x4CD4BB0: __run_exit_handlers (exit.c:108)  
> ==49727==    by 0x4CD4C7D: exit (exit.c:138)  
> ==49727==    by 0x4CBC08E: (below main) (libc_start_call_main.h:74)  
> ==49727==  Block was alloc'd at  
> ==49727==    at 0x4842FB5: operator new(unsigned long) (vg_replace_malloc.c:487)  
> ==49727==    by 0x45BA87: std::__new_allocator<char>::allocate(unsigned long, void const*) (new_allocator.h:151)  
> ==49727==    by 0x44539F: UnknownInlinedFun (allocator.h:196)  
> ==49727==    by 0x44539F: UnknownInlinedFun (alloc_traits.h:478)  
> ==49727==    by 0x44539F: std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::_S_allocate(std::allocator<char>&, unsigned long) (basic_string.h:131)  
> ==49727==    by 0x4452D5: std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::_M_create(unsigned long&, unsigned long) (basic_string.tcc:159)  
> ==49727==    by 0x444F3B: void std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::_M_construct<char const*>(char const*, char const*, std::forward_iterator_tag) (basic_string.tcc:229)  
> ==49727==    by 0x4245A7: std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::basic_string<std::allocator<char> >(char const*, std::allocator<char> const&) (basic_string.h:654)  
> ==49727==    by 0x4A5C7A: __static_initialization_and_destruction_0() (unit_test_parameters.ipp:97)  
> ==49727==    by 0x4AF515: _GLOBAL__sub_I__ZN5boost9unit_test6output22compiler_log_formatter9log_startERSom (test_instrument.cpp:14)  
> ==49727==    by 0x4CBC1C3: call_init (libc-start.c:145)  
> ==49727==    by 0x4CBC1C3: __libc_start_main@@GLIBC_2.34 (libc-start.c:347)  
> ==49727==    by 0x40E944: (below main) (in /home/avatli/workspace/bull/hft-api/build/tests/test_instrument)  
  
**Steps to Reproduce:**  
  
1. Create a minimal test case as shown above.  
2. Compile and link the test case using Boost.Test.  
3. Run the test with Valgrind to observe the error.  
  
Additional Information:  
  
1. Boost version: 1.85  
2. Compiler: g++ (GCC) 14.1.1 20240620 (Red Hat 14.1.1-6)  
3. Operating System: Linux fedora 6.9.5-200.fc40.x86_64

---

## Comment 1

> Username: mborland  
> Created at: 2024-06-27 12:52:03 UTC  
> Url: https://github.com/boostorg/test/issues/419#issuecomment-2194598098  

This seems similar to: https://github.com/boostorg/test/issues/418. Can you try their suggestions?
