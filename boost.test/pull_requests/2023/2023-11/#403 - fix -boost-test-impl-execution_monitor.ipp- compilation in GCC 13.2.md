# #403 fix "boost/test/impl/execution_monitor.ipp" compilation in GCC 13.2 [Closed]

> Username: gh-user-2022  
> Created at: 2023-11-29 09:35:03 UTC  
> Updated at: 2023-11-29 11:00:58 UTC  
> Closed at: 2023-11-29 10:57:38 UTC  
> Url: https://github.com/boostorg/test/pull/403  

Without this fix GCC was giving compile error that PRIxPTR is not defined.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-11-29 10:06:23 UTC  
> Url: https://github.com/boostorg/test/pull/403#issuecomment-1831587184  

Do you have a reproducer? I just ran the tests on Fedora 39 with gcc version 13.2.1 20231011 (Red Hat 13.2.1-4) with no issues. Godbolt shows the macro is defined: https://godbolt.org/z/3ETsozcz6

---

## Comment 2

> Username: gh-user-2022  
> Created_at: 2023-11-29 10:50:24 UTC  
> Url: https://github.com/boostorg/test/pull/403#issuecomment-1831658112  

In CentOS 6 when I build the boost-1.83 with GCC 13.2 there is error:  
  
```  
gcc.compile.c++ bin.v2/libs/test/build/gcc-13/release/cxxstd-20-iso/link-static/threading-multi/visibility-hidden/execution_monitor.o  
In file included from libs/test/src/execution_monitor.cpp:16:  
./boost/test/impl/execution_monitor.ipp: In member function `void boost::detail::system_signal_exception::report() const':  
./boost/test/impl/execution_monitor.ipp:435:88: error: expected `)' before `PRIxPTR'  
  435 |                           "signal: illegal opcode; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
      |                                                                                        ^  
      |                                                                                        )  
./boost/test/impl/execution_monitor.ipp:434:25: note: to match this `('  
  434 |             report_error( execution_exception::system_fatal_error,  
      |                         ^  
./boost/test/impl/execution_monitor.ipp:205:1: note: `PRIxPTR' is defined in header `<cinttypes>'; did you forget to `#include <cinttypes>'?  
  204 | #include <boost/test/detail/suppress_warnings.hpp>  
  +++ |+#include <cinttypes>  
```

---

## Comment 3

> Username: gh-user-2022  
> Created_at: 2023-11-29 10:57:24 UTC  
> Url: https://github.com/boostorg/test/pull/403#issuecomment-1831669396  

I re-checked it on version from the `develop` branch, and it was apparently fixed by adding  
  
```  
// glibc 2.2 - 2.17 required __STDC_FORMAT_MACROS to be defined for use of PRIxPTR  
#  if defined(__GLIBC__) && !((__GLIBC__ > 2) || ((__GLIBC__ == 2) && (__GLIBC_MINOR__ >= 18)))  
#    ifndef __STDC_FORMAT_MACROS  
#      define __STDC_FORMAT_MACROS 1  
#      define BOOST_TEST_DEFINED_STDC_FORMAT_MACROS  
#    endif  
#  endif  
```

---

## Comment 4

> Username: mborland  
> Created_at: 2023-11-29 11:00:57 UTC  
> Url: https://github.com/boostorg/test/pull/403#issuecomment-1831674727  

That diff will be in Boost 1.84 which releases in 2 weeks.

---
