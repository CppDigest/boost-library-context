# #424 Add support for loongarch64 [Closed]

> Username: loongson-zn  
> Created at: 2022-02-14 07:39:38 UTC  
> Updated at: 2022-09-19 06:37:08 UTC  
> Closed at: 2022-09-19 06:23:22 UTC  
> Url: https://github.com/boostorg/config/pull/424  

Add support for loongarch64, please review, thanks!  
  
Signed-off-by: Zhang Na <zhangna@loongson.cn>  
Reviewed-by: Wang Xuerui [git@xen0n.name](mailto:git@xen0n.name)  
Reviewed-by: Zhu Yaliang [zhuyaliang@loongson.cn](mailto:zhuyaliang@loongson.cn)

---

## Comment 1

> Username: loongson-zn  
> Created_at: 2022-02-24 03:57:39 UTC  
> Url: https://github.com/boostorg/config/pull/424#issuecomment-1049470000  

test result:  
```  
zn@la-11:~/github/boost-zn/libs/config/test$ arch  
loongarch64  
zn@la-11:~/github/boost-zn/libs/config/test$ ../../../b2   
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : loongarch (cached) [1]  
    - symlinks supported       : yes (cached)  
    - has_atomic_lib builds    : yes (cached) [2]  
    - has_pthread_lib builds   : yes (cached) [2]  
    - has_rt_lib builds        : yes (cached) [2]  
    - has_atomic_lib builds    : yes (cached) [3]  
    - has_pthread_lib builds   : yes (cached) [3]  
    - has_rt_lib builds        : yes (cached) [3]  
    - has_atomic_lib builds    : yes (cached) [4]  
    - has_pthread_lib builds   : yes (cached) [4]  
    - has_rt_lib builds        : yes (cached) [4]  
    - check_memory builds      : yes (cached) [4]  
    - has_atomic_lib builds    : yes (cached) [5]  
    - has_pthread_lib builds   : yes (cached) [5]  
    - has_rt_lib builds        : yes (cached) [5]  
    - Clang implicit fallthrough : no  (cached) [2]  
    - int128                   : yes (cached) [2]  
    - cxx11_constexpr          : yes (cached) [2]  
    - cxx11_user_defined_literals : yes (cached) [2]  
    - cpp_lib_complex_udls     : yes (cached) [2]  
    - cpp_range_based_for_17   : no  (cached) [2]  
    - int128                   : yes (cached) [6]  
    - cxx11_constexpr          : yes (cached) [6]  
    - cxx11_user_defined_literals : yes (cached) [6]  
    - cpp_lib_complex_udls     : yes (cached) [6]  
    - cpp_range_based_for_17   : no  (cached) [6]  
  
[1] gcc-8  
[2] gcc-8/debug/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
[3] gcc-8/debug/python-2.7/threadapi-pthread/visibility-hidden  
[4] gcc-8/debug/python-2.7/rtti-off/threadapi-pthread/threading-multi/visibility-hidden  
[5] gcc-8/debug/exception-handling-off/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
[6] gcc-8/debug/build-no/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
...patience...  
...found 806 targets...  
...updating 85 targets...  
gcc.compile.c ../../../bin.v2/libs/config/test/config_test_c.test/gcc-8/debug/threading-multi/visibility-hidden/config_test_c.o  
**passed** ../../../bin.v2/libs/config/test/config_test_c.test/gcc-8/debug/threading-multi/visibility-hidden/config_test_c.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/config_test_no_except.test/gcc-8/debug/exception-handling-off/threading-multi/visibility-hidden/config_test.o  
In file included from /usr/include/c++/8/backward/hash_set:60,  
                 from boost_has_hash.ipp:13,  
                 from config_test.cpp:1007:  
/usr/include/c++/8/backward/backward_warning.h:32:2: warning: #warning This file includes at least one deprecated or antiquated header which may be removed without further notice at a future date. Please use a non-deprecated interface with equivalent functionality instead. For a listing of replacement headers and interfaces, consult the file backward_warning.h. To disable this warning use -Wno-deprecated. [-Wcpp]  
 #warning \  
  ^~~~~~~  
In file included from config_test.cpp:246:  
boost_no_cxx11_inline_namespaces.ipp: In function ‘int boost_no_cxx11_inline_namespaces::test()’:  
boost_no_cxx11_inline_namespaces.ipp:24:15: warning: self-comparison always evaluates to true [-Wtautological-compare]  
     if (&data == &my_ns::data)  
         ~~~~~~^~~~~~~~~  
In file included from config_test.cpp:1012:  
boost_has_int128.ipp: In function ‘int boost_has_int128::test()’:  
boost_has_int128.ipp:67:31: warning: variable ‘kk’ set but not used [-Wunused-but-set-variable]  
    my_uint128_t ii(2), jj(1), kk;  
                               ^~  
gcc.link ../../../bin.v2/libs/config/test/config_test_no_except.test/gcc-8/debug/exception-handling-off/threading-multi/visibility-hidden/config_test_no_except  
testing.capture-output ../../../bin.v2/libs/config/test/config_test_no_except.test/gcc-8/debug/exception-handling-off/threading-multi/visibility-hidden/config_test_no_except.run  
**passed** ../../../bin.v2/libs/config/test/config_test_no_except.test/gcc-8/debug/exception-handling-off/threading-multi/visibility-hidden/config_test_no_except.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/config_test_no_rtti.test/gcc-8/debug/rtti-off/threading-multi/visibility-hidden/config_test.o  
In file included from /usr/include/c++/8/backward/hash_set:60,  
                 from boost_has_hash.ipp:13,  
                 from config_test.cpp:1007:  
/usr/include/c++/8/backward/backward_warning.h:32:2: warning: #warning This file includes at least one deprecated or antiquated header which may be removed without further notice at a future date. Please use a non-deprecated interface with equivalent functionality instead. For a listing of replacement headers and interfaces, consult the file backward_warning.h. To disable this warning use -Wno-deprecated. [-Wcpp]  
 #warning \  
  ^~~~~~~  
In file included from config_test.cpp:246:  
boost_no_cxx11_inline_namespaces.ipp: In function ‘int boost_no_cxx11_inline_namespaces::test()’:  
boost_no_cxx11_inline_namespaces.ipp:24:15: warning: self-comparison always evaluates to true [-Wtautological-compare]  
     if (&data == &my_ns::data)  
         ~~~~~~^~~~~~~~~  
In file included from config_test.cpp:1012:  
boost_has_int128.ipp: In function ‘int boost_has_int128::test()’:  
boost_has_int128.ipp:67:31: warning: variable ‘kk’ set but not used [-Wunused-but-set-variable]  
    my_uint128_t ii(2), jj(1), kk;  
                               ^~  
gcc.compile.c++ ../../../bin.v2/libs/config/test/config_test_threaded.test/gcc-8/debug/threading-multi/visibility-hidden/config_test.o  
In file included from /usr/include/c++/8/backward/hash_set:60,  
                 from boost_has_hash.ipp:13,  
                 from config_test.cpp:1007:  
/usr/include/c++/8/backward/backward_warning.h:32:2: warning: #warning This file includes at least one deprecated or antiquated header which may be removed without further notice at a future date. Please use a non-deprecated interface with equivalent functionality instead. For a listing of replacement headers and interfaces, consult the file backward_warning.h. To disable this warning use -Wno-deprecated. [-Wcpp]  
 #warning \  
  ^~~~~~~  
In file included from config_test.cpp:246:  
boost_no_cxx11_inline_namespaces.ipp: In function ‘int boost_no_cxx11_inline_namespaces::test()’:  
boost_no_cxx11_inline_namespaces.ipp:24:15: warning: self-comparison always evaluates to true [-Wtautological-compare]  
     if (&data == &my_ns::data)  
         ~~~~~~^~~~~~~~~  
In file included from config_test.cpp:1012:  
boost_has_int128.ipp: In function ‘int boost_has_int128::test()’:  
boost_has_int128.ipp:67:31: warning: variable ‘kk’ set but not used [-Wunused-but-set-variable]  
    my_uint128_t ii(2), jj(1), kk;  
                               ^~  
gcc.link ../../../bin.v2/libs/config/test/config_test_no_rtti.test/gcc-8/debug/rtti-off/threading-multi/visibility-hidden/config_test_no_rtti  
testing.capture-output ../../../bin.v2/libs/config/test/config_test_no_rtti.test/gcc-8/debug/rtti-off/threading-multi/visibility-hidden/config_test_no_rtti.run  
**passed** ../../../bin.v2/libs/config/test/config_test_no_rtti.test/gcc-8/debug/rtti-off/threading-multi/visibility-hidden/config_test_no_rtti.test  
gcc.link ../../../bin.v2/libs/config/test/config_test_threaded.test/gcc-8/debug/threading-multi/visibility-hidden/config_test_threaded  
testing.capture-output ../../../bin.v2/libs/config/test/config_test_threaded.test/gcc-8/debug/threading-multi/visibility-hidden/config_test_threaded.run  
**passed** ../../../bin.v2/libs/config/test/config_test_threaded.test/gcc-8/debug/threading-multi/visibility-hidden/config_test_threaded.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/config_test.test/gcc-8/debug/visibility-hidden/config_test.o  
In file included from /usr/include/c++/8/backward/hash_set:60,  
                 from boost_has_hash.ipp:13,  
                 from config_test.cpp:1007:  
/usr/include/c++/8/backward/backward_warning.h:32:2: warning: #warning This file includes at least one deprecated or antiquated header which may be removed without further notice at a future date. Please use a non-deprecated interface with equivalent functionality instead. For a listing of replacement headers and interfaces, consult the file backward_warning.h. To disable this warning use -Wno-deprecated. [-Wcpp]  
 #warning \  
  ^~~~~~~  
In file included from config_test.cpp:246:  
boost_no_cxx11_inline_namespaces.ipp: In function ‘int boost_no_cxx11_inline_namespaces::test()’:  
boost_no_cxx11_inline_namespaces.ipp:24:15: warning: self-comparison always evaluates to true [-Wtautological-compare]  
     if (&data == &my_ns::data)  
         ~~~~~~^~~~~~~~~  
In file included from config_test.cpp:1012:  
boost_has_int128.ipp: In function ‘int boost_has_int128::test()’:  
boost_has_int128.ipp:67:31: warning: variable ‘kk’ set but not used [-Wunused-but-set-variable]  
    my_uint128_t ii(2), jj(1), kk;  
                               ^~  
gcc.compile.c++ ../../../bin.v2/libs/config/test/config_info.test/gcc-8/debug/visibility-hidden/config_info.o  
gcc.link ../../../bin.v2/libs/config/test/config_info.test/gcc-8/debug/visibility-hidden/config_info  
testing.capture-output ../../../bin.v2/libs/config/test/config_info.test/gcc-8/debug/visibility-hidden/config_info.run  
**passed** ../../../bin.v2/libs/config/test/config_info.test/gcc-8/debug/visibility-hidden/config_info.test  
gcc.link ../../../bin.v2/libs/config/test/config_test.test/gcc-8/debug/visibility-hidden/config_test  
testing.capture-output ../../../bin.v2/libs/config/test/config_test.test/gcc-8/debug/visibility-hidden/config_test.run  
**passed** ../../../bin.v2/libs/config/test/config_test.test/gcc-8/debug/visibility-hidden/config_test.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/config_info_no_rtti.test/gcc-8/debug/rtti-off/threading-multi/visibility-hidden/config_info.o  
gcc.compile.c++ ../../../bin.v2/libs/config/test/config_info_threaded.test/gcc-8/debug/threading-multi/visibility-hidden/config_info.o  
gcc.link ../../../bin.v2/libs/config/test/config_info_no_rtti.test/gcc-8/debug/rtti-off/threading-multi/visibility-hidden/config_info_no_rtti  
testing.capture-output ../../../bin.v2/libs/config/test/config_info_no_rtti.test/gcc-8/debug/rtti-off/threading-multi/visibility-hidden/config_info_no_rtti.run  
**passed** ../../../bin.v2/libs/config/test/config_info_no_rtti.test/gcc-8/debug/rtti-off/threading-multi/visibility-hidden/config_info_no_rtti.test  
gcc.link ../../../bin.v2/libs/config/test/config_info_threaded.test/gcc-8/debug/threading-multi/visibility-hidden/config_info_threaded  
testing.capture-output ../../../bin.v2/libs/config/test/config_info_threaded.test/gcc-8/debug/threading-multi/visibility-hidden/config_info_threaded.run  
**passed** ../../../bin.v2/libs/config/test/config_info_threaded.test/gcc-8/debug/threading-multi/visibility-hidden/config_info_threaded.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/abi_test.test/gcc-8/debug/threading-multi/visibility-hidden/abi/abi_test.o  
gcc.compile.c++ ../../../bin.v2/libs/config/test/abi_test.test/gcc-8/debug/threading-multi/visibility-hidden/abi/main.o  
gcc.link ../../../bin.v2/libs/config/test/abi_test.test/gcc-8/debug/threading-multi/visibility-hidden/abi_test  
testing.capture-output ../../../bin.v2/libs/config/test/abi_test.test/gcc-8/debug/threading-multi/visibility-hidden/abi_test.run  
**passed** ../../../bin.v2/libs/config/test/abi_test.test/gcc-8/debug/threading-multi/visibility-hidden/abi_test.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/config_info_no_except.test/gcc-8/debug/exception-handling-off/threading-multi/visibility-hidden/config_info.o  
gcc.compile.c++ ../../../bin.v2/libs/config/test/config_link_test.test/gcc-8/debug/visibility-hidden/link/main.o  
gcc.link ../../../bin.v2/libs/config/test/config_info_no_except.test/gcc-8/debug/exception-handling-off/threading-multi/visibility-hidden/config_info_no_except  
testing.capture-output ../../../bin.v2/libs/config/test/config_info_no_except.test/gcc-8/debug/exception-handling-off/threading-multi/visibility-hidden/config_info_no_except.run  
**passed** ../../../bin.v2/libs/config/test/config_info_no_except.test/gcc-8/debug/exception-handling-off/threading-multi/visibility-hidden/config_info_no_except.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/test_thread_fail1.test/gcc-8/debug/threading-multi/visibility-hidden/test_thread_fail1.o  
In file included from threads/test_thread_fail1.cpp:9:  
../../../boost/config/requires_threads.hpp:29:4: error: #error "Threading support unavaliable: it has been explicitly disabled with BOOST_DISABLE_THREADS"  
 #  error "Threading support unavaliable: it has been explicitly disabled with BOOST_DISABLE_THREADS"  
    ^~~~~  
(failed-as-expected) ../../../bin.v2/libs/config/test/test_thread_fail1.test/gcc-8/debug/threading-multi/visibility-hidden/test_thread_fail1.o  
**passed** ../../../bin.v2/libs/config/test/test_thread_fail1.test/gcc-8/debug/threading-multi/visibility-hidden/test_thread_fail1.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/test_thread_fail2.test/gcc-8/debug/threading-multi/visibility-hidden/test_thread_fail2.o  
In file included from threads/test_thread_fail2.cpp:12:  
../../../boost/config/requires_threads.hpp:47:5: error: #error "Compiler threading support is not turned on. Please set the correct command line options for threading: -pthread (Linux), -pthreads (Solaris) or -mthreads (Mingw32)"  
 #   error "Compiler threading support is not turned on. Please set the correct command line options for threading: -pthread (Linux), -pthreads (Solaris) or -mthreads (Mingw32)"  
     ^~~~~  
(failed-as-expected) ../../../bin.v2/libs/config/test/test_thread_fail2.test/gcc-8/debug/threading-multi/visibility-hidden/test_thread_fail2.o  
**passed** ../../../bin.v2/libs/config/test/test_thread_fail2.test/gcc-8/debug/threading-multi/visibility-hidden/test_thread_fail2.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/boost_fallthrough_test.test/gcc-8/debug/threading-multi/visibility-hidden/boost_fallthrough_test.o  
**passed** ../../../bin.v2/libs/config/test/boost_fallthrough_test.test/gcc-8/debug/threading-multi/visibility-hidden/boost_fallthrough_test.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/helper_macro_test.test/gcc-8/debug/threading-multi/visibility-hidden/helper_macro_test.o  
In file included from ../../../boost/config.hpp:61,  
                 from helper_macro_test.cpp:7:  
helper_macro_test.cpp: In function ‘int main()’:  
helper_macro_test.cpp:72:35: warning: unused variable ‘value’ [-Wunused-variable]  
       BOOST_STATIC_CONSTANT(bool, value = 0);  
                                   ^~~~~  
helper_macro_test.cpp:78:24: warning: unused variable ‘s’ [-Wunused-variable]  
       nodiscard_struct s;  
                        ^  
helper_macro_test.cpp:79:17: warning: unused variable ‘no_un’ [-Wunused-variable]  
       no_unique no_un;  
                 ^~~~~  
**passed** ../../../bin.v2/libs/config/test/helper_macro_test.test/gcc-8/debug/threading-multi/visibility-hidden/helper_macro_test.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/math_info.test/gcc-8/debug/threading-multi/visibility-hidden/math_info.o  
gcc.compile.c++ ../../../bin.v2/libs/config/test/limits_test.test/gcc-8/debug/threading-multi/visibility-hidden/limits_test.o  
gcc.link ../../../bin.v2/libs/config/test/math_info.test/gcc-8/debug/threading-multi/visibility-hidden/math_info  
testing.capture-output ../../../bin.v2/libs/config/test/math_info.test/gcc-8/debug/threading-multi/visibility-hidden/math_info.run  
**passed** ../../../bin.v2/libs/config/test/math_info.test/gcc-8/debug/threading-multi/visibility-hidden/math_info.test  
gcc.link ../../../bin.v2/libs/config/test/limits_test.test/gcc-8/debug/threading-multi/visibility-hidden/limits_test  
testing.capture-output ../../../bin.v2/libs/config/test/limits_test.test/gcc-8/debug/threading-multi/visibility-hidden/limits_test.run  
**passed** ../../../bin.v2/libs/config/test/limits_test.test/gcc-8/debug/threading-multi/visibility-hidden/limits_test.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/cstdint_include_test.test/gcc-8/debug/threading-multi/visibility-hidden/cstdint_include_test.o  
gcc.compile.c++ ../../../bin.v2/libs/config/test/link/gcc-8/debug/visibility-hidden/link_test.o  
**passed** ../../../bin.v2/libs/config/test/cstdint_include_test.test/gcc-8/debug/threading-multi/visibility-hidden/cstdint_include_test.test  
gcc.link.dll ../../../bin.v2/libs/config/test/link/gcc-8/debug/visibility-hidden/liblink_test.so.1.79.0  
gcc.compile.c++ ../../../bin.v2/libs/config/test/cstdint_test.test/gcc-8/debug/threading-multi/visibility-hidden/cstdint_test.o  
gcc.link ../../../bin.v2/libs/config/test/config_link_test.test/gcc-8/debug/visibility-hidden/config_link_test  
testing.capture-output ../../../bin.v2/libs/config/test/config_link_test.test/gcc-8/debug/visibility-hidden/config_link_test.run  
**passed** ../../../bin.v2/libs/config/test/config_link_test.test/gcc-8/debug/visibility-hidden/config_link_test.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/pragma_message_test.test/gcc-8/debug/threading-multi/visibility-hidden/pragma_message_test.o  
pragma_message_test.cpp:10:37: note: #pragma message: first message  
 BOOST_PRAGMA_MESSAGE("first message")  
                                     ^  
pragma_message_test.cpp:13:26: note: #pragma message: second message  
 BOOST_PRAGMA_MESSAGE(MSG2)  
                          ^  
pragma_message_test.cpp:18:43: note: #pragma message: third message  
 BOOST_PRAGMA_MESSAGE(BOOST_STRINGIZE(MSG3))  
                                           ^  
**passed** ../../../bin.v2/libs/config/test/pragma_message_test.test/gcc-8/debug/threading-multi/visibility-hidden/pragma_message_test.test  
gcc.link ../../../bin.v2/libs/config/test/cstdint_test.test/gcc-8/debug/threading-multi/visibility-hidden/cstdint_test  
testing.capture-output ../../../bin.v2/libs/config/test/cstdint_test.test/gcc-8/debug/threading-multi/visibility-hidden/cstdint_test.run  
**passed** ../../../bin.v2/libs/config/test/cstdint_test.test/gcc-8/debug/threading-multi/visibility-hidden/cstdint_test.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/header_deprecated_test.test/gcc-8/debug/threading-multi/visibility-hidden/header_deprecated_test.o  
header_deprecated_test.cpp:10:56: note: #pragma message: This header is deprecated. Use <boost/config/workaround.hpp> instead.  
 BOOST_HEADER_DEPRECATED("<boost/config/workaround.hpp>")  
                                                        ^  
header_deprecated_test.cpp:13:36: note: #pragma message: This header is deprecated. Use the suitable component header instead.  
 BOOST_HEADER_DEPRECATED(ALTERNATIVE)  
                                    ^  
header_deprecated_test.cpp:18:48: note: #pragma message: This header is deprecated. Use <boost/config/workaround.hpp> instead.  
 BOOST_HEADER_DEPRECATED(BOOST_STRINGIZE(HEADER))  
                                                ^  
**passed** ../../../bin.v2/libs/config/test/header_deprecated_test.test/gcc-8/debug/threading-multi/visibility-hidden/header_deprecated_test.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/boost_override_test.test/gcc-8/debug/threading-multi/visibility-hidden/boost_override_test.o  
**passed** ../../../bin.v2/libs/config/test/boost_override_test.test/gcc-8/debug/threading-multi/visibility-hidden/boost_override_test.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/cstdint_test2.test/gcc-8/debug/threading-multi/visibility-hidden/cstdint_test2.o  
gcc.link ../../../bin.v2/libs/config/test/cstdint_test2.test/gcc-8/debug/threading-multi/visibility-hidden/cstdint_test2  
testing.capture-output ../../../bin.v2/libs/config/test/cstdint_test2.test/gcc-8/debug/threading-multi/visibility-hidden/cstdint_test2.run  
**passed** ../../../bin.v2/libs/config/test/cstdint_test2.test/gcc-8/debug/threading-multi/visibility-hidden/cstdint_test2.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/helper_macros_test.test/gcc-8/debug/threading-multi/visibility-hidden/helper_macros_test.o  
gcc.link ../../../bin.v2/libs/config/test/helper_macros_test.test/gcc-8/debug/threading-multi/visibility-hidden/helper_macros_test  
testing.capture-output ../../../bin.v2/libs/config/test/helper_macros_test.test/gcc-8/debug/threading-multi/visibility-hidden/helper_macros_test.run  
**passed** ../../../bin.v2/libs/config/test/helper_macros_test.test/gcc-8/debug/threading-multi/visibility-hidden/helper_macros_test.test  
gcc.compile.c++ ../../../bin.v2/libs/config/test/gcc-8/debug/threading-multi/visibility-hidden/config_info.o  
gcc.link ../../../bin.v2/libs/config/test/gcc-8/debug/threading-multi/visibility-hidden/config_info_travis  
...updated 85 targets...  
```

---

## Comment 2

> Username: loongson-zn  
> Created_at: 2022-03-02 02:54:58 UTC  
> Url: https://github.com/boostorg/config/pull/424#issuecomment-1056098933  

Hi @jzmaddock ,  LoongArch is a new CPU architecture.  Here are some links to LoongArch. I hope you can review and accept this patch, thanks.  
Documentations:  
  - ISA:  
    https://loongson.github.io/LoongArch-Documentation/LoongArch-Vol1-EN.html  
  - ABI:  
    https://loongson.github.io/LoongArch-Documentation/LoongArch-ELF-ABI-EN.html  
  - More docs can be found at:  
    https://loongson.github.io/LoongArch-Documentation/README-EN.html

---

## Comment 3

> Username: yetist  
> Created_at: 2022-09-19 06:09:03 UTC  
> Url: https://github.com/boostorg/config/pull/424#issuecomment-1250605667  

@loongson-zn  please resolve the conflicts.

---

## Comment 4

> Username: loongson-zn  
> Created_at: 2022-09-19 06:37:08 UTC  
> Url: https://github.com/boostorg/config/pull/424#issuecomment-1250623507  

https://github.com/boostorg/config/pull/446#issue-1377474412

---
