# #436 - Build error: `execution_monitor.ipp: error: expected ')' before 'PRIxPTR'` [Closed]

> Username: barracuda156  
> Created at: 2025-01-17 06:26:35 UTC  
> Updated at: 2025-01-26 20:12:29 UTC  
> Closed at: 2025-01-22 18:54:06 UTC  
> Url: https://github.com/boostorg/test/issues/436  

The following header has an inaccurate condition to define `__STDC_FORMAT_MACROS`:  
  
https://github.com/boostorg/test/blob/fcd634a534835068c2f160101cd19c23b7360dcb/include/boost/test/impl/execution_monitor.ipp#L197-L212  
  
So I get this failure on macOS, for example:  
```  
:info:build ./boost/test/impl/execution_monitor.ipp:444:25: note: to match this '('  
:info:build   444 |             report_error( execution_exception::system_fatal_error,  
:info:build       |                         ^  
:info:build ./boost/test/impl/execution_monitor.ipp:215:1: note: 'PRIxPTR' is defined in header '<cinttypes>'; this is probably fixable by adding '#include <cinttypes>'  
:info:build   214 | #include <boost/test/detail/suppress_warnings.hpp>  
:info:build   +++ |+#include <cinttypes>  
:info:build   215 |   
:info:build ./boost/test/impl/execution_monitor.ipp:445:86: warning: spurious trailing '%' in format [-Wformat=]  
:info:build   445 |                           "signal: illegal opcode; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                      ^  
:info:build ./boost/test/impl/execution_monitor.ipp:445:27: warning: too many arguments for format [-Wformat-extra-args]  
:info:build   445 |                           "signal: illegal opcode; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:450:86: error: expected ')' before 'PRIxPTR'  
:info:build   450 |                           "signal: illegal trap; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                      ^  
:info:build       |                                                                                      )  
:info:build ./boost/test/impl/execution_monitor.ipp:449:25: note: to match this '('  
:info:build   449 |             report_error( execution_exception::system_fatal_error,  
:info:build       |                         ^  
:info:build ./boost/test/impl/execution_monitor.ipp:206:30: note: 'PRIxPTR' is defined in header '<cinttypes>'; this is probably fixable by adding '#include <cinttypes>'  
:info:build   206 | #  define BOOST_TEST_PRIxPTR PRIxPTR  
:info:build       |                              ^~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:450:87: note: in expansion of macro 'BOOST_TEST_PRIxPTR'  
:info:build   450 |                           "signal: illegal trap; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                       ^~~~~~~~~~~~~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:450:84: warning: spurious trailing '%' in format [-Wformat=]  
:info:build   450 |                           "signal: illegal trap; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                    ^  
:info:build ./boost/test/impl/execution_monitor.ipp:450:27: warning: too many arguments for format [-Wformat-extra-args]  
:info:build   450 |                           "signal: illegal trap; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:455:93: error: expected ')' before 'PRIxPTR'  
:info:build   455 |                           "signal: privileged register; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                             ^  
:info:build       |                                                                                             )  
:info:build ./boost/test/impl/execution_monitor.ipp:454:25: note: to match this '('  
:info:build   454 |             report_error( execution_exception::system_fatal_error,  
:info:build       |                         ^  
:info:build ./boost/test/impl/execution_monitor.ipp:206:30: note: 'PRIxPTR' is defined in header '<cinttypes>'; this is probably fixable by adding '#include <cinttypes>'  
:info:build   206 | #  define BOOST_TEST_PRIxPTR PRIxPTR  
:info:build       |                              ^~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:455:94: note: in expansion of macro 'BOOST_TEST_PRIxPTR'  
:info:build   455 |                           "signal: privileged register; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                              ^~~~~~~~~~~~~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:455:91: warning: spurious trailing '%' in format [-Wformat=]  
:info:build   455 |                           "signal: privileged register; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                           ^  
:info:build ./boost/test/impl/execution_monitor.ipp:455:27: warning: too many arguments for format [-Wformat-extra-args]  
:info:build   455 |                           "signal: privileged register; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:460:94: error: expected ')' before 'PRIxPTR'  
:info:build   460 |                           "signal: internal stack error; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                              ^  
:info:build       |                                                                                              )  
:info:build ./boost/test/impl/execution_monitor.ipp:459:25: note: to match this '('  
:info:build   459 |             report_error( execution_exception::system_fatal_error,  
:info:build       |                         ^  
:info:build ./boost/test/impl/execution_monitor.ipp:206:30: note: 'PRIxPTR' is defined in header '<cinttypes>'; this is probably fixable by adding '#include <cinttypes>'  
:info:build   206 | #  define BOOST_TEST_PRIxPTR PRIxPTR  
:info:build       |                              ^~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:460:95: note: in expansion of macro 'BOOST_TEST_PRIxPTR'  
:info:build   460 |                           "signal: internal stack error; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                               ^~~~~~~~~~~~~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:460:92: warning: spurious trailing '%' in format [-Wformat=]  
:info:build   460 |                           "signal: internal stack error; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                            ^  
:info:build ./boost/test/impl/execution_monitor.ipp:460:27: warning: too many arguments for format [-Wformat-extra-args]  
:info:build   460 |                           "signal: internal stack error; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:466:89: error: expected ')' before 'PRIxPTR'  
:info:build   466 |                           "signal: illegal operand; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                         ^  
:info:build       |                                                                                         )  
:info:build ./boost/test/impl/execution_monitor.ipp:465:25: note: to match this '('  
:info:build   465 |             report_error( execution_exception::system_fatal_error,  
:info:build       |                         ^  
:info:build ./boost/test/impl/execution_monitor.ipp:206:30: note: 'PRIxPTR' is defined in header '<cinttypes>'; this is probably fixable by adding '#include <cinttypes>'  
:info:build   206 | #  define BOOST_TEST_PRIxPTR PRIxPTR  
:info:build       |                              ^~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:466:90: note: in expansion of macro 'BOOST_TEST_PRIxPTR'  
:info:build   466 |                           "signal: illegal operand; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                          ^~~~~~~~~~~~~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:466:87: warning: spurious trailing '%' in format [-Wformat=]  
:info:build   466 |                           "signal: illegal operand; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                       ^  
:info:build ./boost/test/impl/execution_monitor.ipp:466:27: warning: too many arguments for format [-Wformat-extra-args]  
:info:build   466 |                           "signal: illegal operand; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:471:97: error: expected ')' before 'PRIxPTR'  
:info:build   471 |                           "signal: illegal addressing mode; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                                 ^  
:info:build       |                                                                                                 )  
:info:build ./boost/test/impl/execution_monitor.ipp:470:25: note: to match this '('  
:info:build   470 |             report_error( execution_exception::system_fatal_error,  
:info:build       |                         ^  
:info:build ./boost/test/impl/execution_monitor.ipp:206:30: note: 'PRIxPTR' is defined in header '<cinttypes>'; this is probably fixable by adding '#include <cinttypes>'  
:info:build   206 | #  define BOOST_TEST_PRIxPTR PRIxPTR  
:info:build       |                              ^~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:471:98: note: in expansion of macro 'BOOST_TEST_PRIxPTR'  
:info:build   471 |                           "signal: illegal addressing mode; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                                  ^~~~~~~~~~~~~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:471:95: warning: spurious trailing '%' in format [-Wformat=]  
:info:build   471 |                           "signal: illegal addressing mode; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                               ^  
:info:build ./boost/test/impl/execution_monitor.ipp:471:27: warning: too many arguments for format [-Wformat-extra-args]  
:info:build   471 |                           "signal: illegal addressing mode; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
:info:build ./boost/test/impl/execution_monitor.ipp:476:91: error: expected ')' before 'PRIxPTR'  
:info:build   476 |                           "signal: privileged opcode; address of failing instruction: 0x%" BOOST_TEST_PRIxPTR,  
:info:build       |                                                                                           ^  
:info:build       |      
```  
  
It should not be restricted to Linux, other systems may also need `__STDC_FORMAT_MACROS` before including `inttypes.h`.

---

## Comment 1

> Username: tjanas  
> Created at: 2025-01-22 15:34:43 UTC  
> Url: https://github.com/boostorg/test/issues/436#issuecomment-2607569867  

Since execution_monitor.ipp includes other c++ headers (like `<cstring>` instead of `<string.h>`), is there a reason why the include is for `<inttypes.h>` instead of `<cinttypes>`?

---

## Comment 2

> Username: tjanas  
> Created at: 2025-01-22 16:03:37 UTC  
> Updated at: 2025-01-22 16:37:25 UTC  
> Url: https://github.com/boostorg/test/issues/436#issuecomment-2607643252  

On a related note, can someone add a patch link to https://www.boost.org/users/history/version_1_83_0.html for this issue that was fixed in linux via https://github.com/boostorg/test/commit/cada8c11df0ee360c2a05b0f44daeae6893419e4 ?  
```  
--- a/boost_1_83_0/boost/test/impl/execution_monitor.ipp  
+++ b/boost_1_83_0/boost/test/impl/execution_monitor.ipp  
@@ -194,9 +194,19 @@ namespace { void _set_se_translator( void* ) {} }  
 #  include <boost/core/demangle.hpp>  
 #endif  
  
-#if !defined(BOOST_MSSTL_VERSION) || (BOOST_MSSTL_VERSION >= 120)  
+#if (!defined(BOOST_MSSTL_VERSION) || (BOOST_MSSTL_VERSION >= 120)) && (!defined(__GLIBC__) || ((__GLIBC__ > 2) || ((__GLIBC__ == 2) && (__GLIBC_MINOR__ >= 2))))  
+// glibc 2.2 - 2.17 required __STDC_FORMAT_MACROS to be defined for use of PRIxPTR  
+#  if defined(__GLIBC__) && !((__GLIBC__ > 2) || ((__GLIBC__ == 2) && (__GLIBC_MINOR__ >= 18)))  
+#    ifndef __STDC_FORMAT_MACROS  
+#      define __STDC_FORMAT_MACROS 1  
+#      define BOOST_TEST_DEFINED_STDC_FORMAT_MACROS  
+#    endif  
+#  endif  
 #  include <inttypes.h>  
 #  define BOOST_TEST_PRIxPTR PRIxPTR  
+#  ifdef BOOST_TEST_DEFINED_STDC_FORMAT_MACROS  
+#    undef __STDC_FORMAT_MACROS  
+#  endif  
 #else  
 #  define BOOST_TEST_PRIxPTR "08lx"  
 #endif  
```

---

## Comment 3

> Username: mborland  
> Created at: 2025-01-22 16:22:12 UTC  
> Url: https://github.com/boostorg/test/issues/436#issuecomment-2607690329  

> Since execution_monitor.ipp includes other c++ headers (like `<cstring>` instead of `<string.h>`), is there a reason why the include is for `<inttypes.h>` instead of `<cinttypes>`?  
  
Compatibility with really old toolchains.

---

## Comment 4

> Username: barracuda156  
> Created at: 2025-01-22 16:38:19 UTC  
> Url: https://github.com/boostorg/test/issues/436#issuecomment-2607730634  

> Since execution_monitor.ipp includes other c++ headers (like `<cstring>` instead of `<string.h>`), is there a reason why the include is for `<inttypes.h>` instead of `<cinttypes>`?  
  
Changing the header to `cinttypes` does not solve the problem of incorrect condition for `__STDC_FORMAT_MACROS` in a general case. It will fix the breakage for some compilers, but not for others.

---

## Comment 5

> Username: mborland  
> Created at: 2025-01-22 16:43:18 UTC  
> Url: https://github.com/boostorg/test/issues/436#issuecomment-2607742707  

> > Since execution_monitor.ipp includes other c++ headers (like `<cstring>` instead of `<string.h>`), is there a reason why the include is for `<inttypes.h>` instead of `<cinttypes>`?  
>   
> Changing the header to `cinttypes` does not solve the problem of incorrect condition for `__STDC_FORMAT_MACROS` in a general case. It will fix the breakage for some compilers, but not for others.  
  
Correct. I think #437 should solve the remaining cases like this one on newer toolchains.

---

## Comment 6

> Username: barracuda156  
> Created at: 2025-01-26 20:12:27 UTC  
> Url: https://github.com/boostorg/test/issues/436#issuecomment-2614574383  

@mborland Sorry, this does not work correctly. The issue is that the first macro evaluates as true on macOS, so `inttypes.h` is includes, but the macro for `__STDC_FORMAT_MACROS` is unnecessarily Linux-only, so we get exactly the same error as before: the added fallback is never used.
