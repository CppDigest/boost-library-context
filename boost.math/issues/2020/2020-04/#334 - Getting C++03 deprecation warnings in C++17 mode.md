# #334 - Getting C++03 deprecation warnings in C++17 mode [Closed]

> Username: NAThompson  
> Created at: 2020-04-02 09:18:58 UTC  
> Updated at: 2020-04-03 14:47:28 UTC  
> Closed at: 2020-04-03 14:47:28 UTC  
> Url: https://github.com/boostorg/math/issues/334  

I'm confused about this:  
  
```  
../../../b2  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached)  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes (cached)  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes (cached)  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes (cached)  
    - Boost.Config Feature Check: cxx11_lambdas : yes (cached)  
    - Boost.Config Feature Check: cxx11_numeric_limits : yes (cached)  
    - GCC libquadmath and __float128 support : no  (cached)  
    - Boost.Config Feature Check: cxx11_explicit_conversion_operators : yes (cached)  
    - Boost.Config Feature Check: cxx11_smart_ptr : yes (cached)  
    - Boost.Config Feature Check: cxx11_function_template_default_args : yes (cached)  
    - Boost.Config Feature Check: cxx11_unified_initialization_syntax : yes (cached)  
    - Boost.Config Feature Check: cxx11_allocator : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_random : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_thread : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_atomic : yes (cached)  
    - Boost.Config Feature Check: cxx11_decltype : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_future : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_chrono : yes (cached)  
    - Boost.Config Feature Check: cxx17_if_constexpr : yes (cached)  
    - Boost.Config Feature Check: cxx17_std_apply : yes (cached)  
    - Boost.Config Feature Check: cxx11_inline_namespaces : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_mutex : yes (cached)  
...patience...  
...patience...  
...patience...  
...found 6953 targets...  
...updating 400 targets...  
darwin.compile.c++ ../../../bin.v2/libs/math/example/hyperexponential_snips.test/darwin-4.2.1/debug/threading-multi/visibility-hidden/hyperexponential_snips.o  
In file included from hyperexponential_snips.cpp:15:  
In file included from ../../../boost/math/distributions/hyperexponential.hpp:20:  
../../../boost/math/tools/cxx03_warn.hpp:24:1: warning: CAUTION: One or more C++11 features were found to be unavailable [-W#pragma-messages]  
BOOST_PRAGMA_MESSAGE("CAUTION: One or more C++11 features were found to be unavailable")  
```  
  
Other info:  
  
```  
$ g++ --version  
Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/c++/4.2.1  
Apple clang version 11.0.0 (clang-1100.0.33.17)  
Target: x86_64-apple-darwin18.7.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-04-02 14:03:54 UTC  
> Url: https://github.com/boostorg/math/issues/334#issuecomment-607866506  

I've pushed an improved version which indicates which macro/defect raised the warning.  Can you try that out?

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-04-02 14:08:52 UTC  
> Updated at: 2020-04-02 14:09:21 UTC  
> Url: https://github.com/boostorg/math/issues/334#issuecomment-607869143  

Just checked:  
  
```  
$ git log  
commit 3c3d09ef41dc95f8ff8ddc2afb2eab09fadef5cb (HEAD -> develop, origin/develop, origin/HEAD)  
Merge: b48cdffd1 645f56fcb  
Author: jzmaddock <john@johnmaddock.co.uk>  
Date:   Thu Apr 2 14:59:42 2020 +0100  
  
    Merge branch 'develop' of https://github.com/boostorg/math into develop  
  
commit b48cdffd1efe6bdd7f8e02f897b7af31e83a2bda  
Author: jzmaddock <john@johnmaddock.co.uk>  
Date:   Thu Apr 2 14:59:16 2020 +0100  
  
    Improve C++03 deprecation diagnostics  
    So we can see which macro is triggering the warning.  
math/example git:(develop) ✗ ../../../b2  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached)  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes (cached)  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes (cached)  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes (cached)  
    - Boost.Config Feature Check: cxx11_lambdas : yes (cached)  
    - Boost.Config Feature Check: cxx11_numeric_limits : yes (cached)  
    - GCC libquadmath and __float128 support : no  (cached)  
    - Boost.Config Feature Check: cxx11_explicit_conversion_operators : yes (cached)  
    - Boost.Config Feature Check: cxx11_smart_ptr : yes (cached)  
    - Boost.Config Feature Check: cxx11_function_template_default_args : yes (cached)  
    - Boost.Config Feature Check: cxx11_unified_initialization_syntax : yes (cached)  
    - Boost.Config Feature Check: cxx11_allocator : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_random : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_thread : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_atomic : yes (cached)  
    - Boost.Config Feature Check: cxx11_decltype : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_future : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_chrono : yes (cached)  
    - Boost.Config Feature Check: cxx17_if_constexpr : no  (cached)  
    - Boost.Config Feature Check: cxx17_std_apply : no  (cached)  
    - Boost.Config Feature Check: cxx11_inline_namespaces : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_mutex : yes (cached)  
...patience...  
...patience...  
...patience...  
...found 6918 targets...  
...updating 299 targets...  
**passed** ../../../bin.v2/libs/math/example/c_error_policy_example.test/darwin-4.2.1/debug/threading-multi/visibility-hidden/c_error_policy_example.test  
**passed** ../../../bin.v2/libs/math/example/find_location_example.test/darwin-4.2.1/debug/threading-multi/visibility-hidden/find_location_example.test  
**passed** ../../../bin.v2/libs/math/example/find_mean_and_sd_normal.test/darwin-4.2.1/debug/threading-multi/visibility-hidden/find_mean_and_sd_normal.test  
**passed** ../../../bin.v2/libs/math/example/find_root_example.test/darwin-4.2.1/debug/threading-multi/visibility-hidden/find_root_example.test  
**passed** ../../../bin.v2/libs/math/example/find_scale_example.test/darwin-4.2.1/debug/threading-multi/visibility-hidden/find_scale_example.test  
darwin.compile.c++ ../../../bin.v2/libs/math/example/lambert_w_basic_example.test/darwin-4.2.1/debug/threading-multi/visibility-hidden/lambert_w_basic_example.o  
In file included from lambert_w_basic_example.cpp:11:  
In file included from ../../../boost/math/special_functions/lambert_w.hpp:56:  
In file included from ../../../boost/math/constants/constants.hpp:11:  
../../../boost/math/tools/cxx03_warn.hpp:99:1: warning: CAUTION: One or more C++11 features were found to be unavailable [-W#pragma-messages]  
BOOST_PRAGMA_MESSAGE("CAUTION: One or more C++11 features were found to be unavailable")  
^  
../../../boost/config/pragma_message.hpp:24:34: note: expanded from macro 'BOOST_PRAGMA_MESSAGE'  
# define BOOST_PRAGMA_MESSAGE(x) _Pragma(BOOST_STRINGIZE(message(x)))  
                                 ^  
<scratch space>:165:2: note: expanded from here  
 message("CAUTION: One or more C++11 features were found to be unavailable")  
 ^  
In file included from lambert_w_basic_example.cpp:11:  
In file included from ../../../boost/math/special_functions/lambert_w.hpp:56:  
In file included from ../../../boost/math/constants/constants.hpp:11:  
../../../boost/math/tools/cxx03_warn.hpp:100:1: warning: CAUTION: Compiling Boost.Math in pre-C++11 conformance modes is now deprecated and will be removed from March 2021. [-W#pragma-messages]  
BOOST_PRAGMA_MESSAGE("CAUTION: Compiling Boost.Math in pre-C++11 conformance modes is now deprecated and will be removed from March 2021.")  
^  
../../../boost/config/pragma_message.hpp:24:34: note: expanded from macro 'BOOST_PRAGMA_MESSAGE'  
# define BOOST_PRAGMA_MESSAGE(x) _Pragma(BOOST_STRINGIZE(message(x)))  
                                 ^  
<scratch space>:168:2: note: expanded from here  
 message("CAUTION: Compiling Boost.Math in pre-C++11 conformance modes is now deprecated and will be removed from March 2021.")  
 ^  
In file included from lambert_w_basic_example.cpp:11:  
In file included from ../../../boost/math/special_functions/lambert_w.hpp:56:  
In file included from ../../../boost/math/constants/constants.hpp:11:  
../../../boost/math/tools/cxx03_warn.hpp:101:1: warning: CAUTION: Define BOOST_MATH_DISABLE_DEPRECATED_03_WARNING to suppress this message. [-W#pragma-messages]  
BOOST_PRAGMA_MESSAGE("CAUTION: Define BOOST_MATH_DISABLE_DEPRECATED_03_WARNING to suppress this message.")  
^  
../../../boost/config/pragma_message.hpp:24:34: note: expanded from macro 'BOOST_PRAGMA_MESSAGE'  
# define BOOST_PRAGMA_MESSAGE(x) _Pragma(BOOST_STRINGIZE(message(x)))  
                                 ^  
<scratch space>:171:2: note: expanded from here  
 message("CAUTION: Define BOOST_MATH_DISABLE_DEPRECATED_03_WARNING to suppress this message.")  
 ^  
In file included from lambert_w_basic_example.cpp:11:  
In file included from ../../../boost/math/special_functions/lambert_w.hpp:56:  
In file included from ../../../boost/math/constants/constants.hpp:11:  
../../../boost/math/tools/cxx03_warn.hpp:102:1: warning: CAUTION: This message was generated due to the define: BOOST_NO_CXX11_NOEXCEPT [-W#pragma-messages]  
BOOST_PRAGMA_MESSAGE("CAUTION: This message was generated due to the define: " BOOST_MATH_CXX03_WARN_REASON)  
^  
../../../boost/config/pragma_message.hpp:24:34: note: expanded from macro 'BOOST_PRAGMA_MESSAGE'  
# define BOOST_PRAGMA_MESSAGE(x) _Pragma(BOOST_STRINGIZE(message(x)))  
                                 ^  
<scratch space>:174:2: note: expanded from here  
 message("CAUTION: This message was generated due to the define: " "BOOST_NO_CXX11_NOEXCEPT")  
 ^  
4 warnings generated.  
```

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-04-02 14:47:47 UTC  
> Url: https://github.com/boostorg/math/issues/334#issuecomment-607891734  

It's just hitting the first check and failing, as a quick sanity check could you please cd into libs/config/test and do a:  
  
../../../b2 -d2 config_info_travis_install  
./config_info_travis  
  
Threre's something odd here...

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-04-02 15:13:27 UTC  
> Url: https://github.com/boostorg/math/issues/334#issuecomment-607907156  

```  
➜  test git:(bbda3f0a) ✗ ./config_info_travis  
Clang version 11.0.0 (clang-1100.0.33.17)  
    __cplusplus                             =199711L  
    __STDC__                                =1  
    __GNUC__                                =4  
    __GNUC_MINOR__                          =2  
    __GNUC_PATCHLEVEL__                     =1  
    __GNUG__                                =4  
    __VERSION__                             ="4.2.1 Compatible Apple LLVM 11.0.0 (clang-1100.0.33.17)"  
    __REGISTER_PREFIX__                      [no value]  
    __USER_LABEL_PREFIX__                   =_  
    __EXCEPTIONS                            =1  
    __GXX_RTTI                              =1  
    __amd64__                               =1  
    __LP64__                                =1  
    __MACH__                                =1  
    __APPLE__                               =1  
    __APPLE_CC__                            =6000  
    __clang__                               =1  
    __clang_major__                         =11  
    __clang_minor__                         =0  
    __clang_version__                       ="11.0.0 (clang-1100.0.33.17)"  
    __clang_patchlevel__                    =0  
    __STDC_HOSTED__                         =1  
  
  
*********************************************************************  
  
libc++ version 8000  
    _LIBCPP_VERSION                         =8000  
  
  
*********************************************************************  
  
Detected Platform: Mac OS  
    Type char is signed  
    Type wchar_t is signed  
    byte order for type short               =0 8  
    byte order for type int                 =0 8 16 24  
    byte order for type long                =0 8 16 24 32 40 48 56  
    sizeof(wchar_t)                         =4  
    sizeof(short)                           =2  
    sizeof(int)                             =4  
    sizeof(long)                            =8  
    sizeof(size_t)                          =8  
    sizeof(ptrdiff_t)                       =8  
    sizeof(void*)                           =8  
    sizeof(void(*)(void))                   =8  
    sizeof(float)                           =4  
    sizeof(double)                          =8  
    sizeof(long double)                     =16  
    CHAR_BIT                                =8  
    CHAR_MAX                                =127  
    WCHAR_MAX                               =2147483647  
    SHRT_MAX                                =32767  
    INT_MAX                                 =2147483647  
    LONG_MAX                                =9223372036854775807L  
    LLONG_MAX                               =0x7fffffffffffffffLL  
    LONG_LONG_MAX                           =9223372036854775807LL  
    ULLONG_MAX                              =0xffffffffffffffffULL  
    ULONG_LONG_MAX                          =(9223372036854775807LL*2ULL+1ULL)  
    _POSIX_ADVISORY_INFO                    =(-1)  
    _POSIX_ASYNCHRONOUS_IO                  =(-1)  
    _POSIX_BARRIERS                         =(-1)  
    _POSIX_CHOWN_RESTRICTED                 =200112L  
    _POSIX_CLOCK_SELECTION                  =(-1)  
    _POSIX_CPUTIME                          =(-1)  
    _POSIX_FSYNC                            =200112L  
    _POSIX_JOB_CONTROL                      =200112L  
    _POSIX_MAPPED_FILES                     =200112L  
    _POSIX_MEMLOCK                          =(-1)  
    _POSIX_MEMLOCK_RANGE                    =(-1)  
    _POSIX_MEMORY_PROTECTION                =200112L  
    _POSIX_MESSAGE_PASSING                  =(-1)  
    _POSIX_MONOTONIC_CLOCK                  =(-1)  
    _POSIX_NO_TRUNC                         =200112L  
    _POSIX_PRIORITIZED_IO                   =(-1)  
    _POSIX_PRIORITY_SCHEDULING              =(-1)  
    _POSIX_RAW_SOCKETS                      =(-1)  
    _POSIX_READER_WRITER_LOCKS              =200112L  
    _POSIX_REALTIME_SIGNALS                 =(-1)  
    _POSIX_REGEXP                           =200112L  
    _POSIX_SAVED_IDS                        =200112L  
    _POSIX_SEMAPHORES                       =(-1)  
    _POSIX_SHARED_MEMORY_OBJECTS            =(-1)  
    _POSIX_SHELL                            =200112L  
    _POSIX_SPAWN                            =(-1)  
    _POSIX_SPIN_LOCKS                       =(-1)  
    _POSIX_SPORADIC_SERVER                  =(-1)  
    _POSIX_SYNCHRONIZED_IO                  =(-1)  
    _POSIX_THREAD_ATTR_STACKADDR            =200112L  
    _POSIX_THREAD_ATTR_STACKSIZE            =200112L  
    _POSIX_THREAD_CPUTIME                   =(-1)  
    _POSIX_THREAD_PRIO_INHERIT              =(-1)  
    _POSIX_THREAD_PRIO_PROTECT              =(-1)  
    _POSIX_THREAD_PRIORITY_SCHEDULING       =(-1)  
    _POSIX_THREAD_PROCESS_SHARED            =200112L  
    _POSIX_THREAD_SAFE_FUNCTIONS            =200112L  
    _POSIX_THREAD_SPORADIC_SERVER           =(-1)  
    _POSIX_THREADS                          =200112L  
    _POSIX_TIMEOUTS                         =(-1)  
    _POSIX_TIMERS                           =(-1)  
    _POSIX_TRACE                            =(-1)  
    _POSIX_TRACE_EVENT_FILTER               =(-1)  
    _POSIX_TRACE_INHERIT                    =(-1)  
    _POSIX_TRACE_LOG                        =(-1)  
    _POSIX_TYPED_MEMORY_OBJECTS             =(-1)  
    _POSIX_VDISABLE                         =((unsigned char)'\377')  
    _POSIX_VERSION                          =200112L  
    _POSIX2_C_BIND                          =200112L  
    _POSIX2_C_DEV                           =200112L  
    _POSIX2_CHAR_TERM                       =200112L  
    _POSIX2_FORT_DEV                        =(-1)  
    _POSIX2_FORT_RUN                        =200112L  
    _POSIX2_LOCALEDEF                       =200112L  
    _POSIX2_PBS                             =(-1)  
    _POSIX2_PBS_ACCOUNTING                  =(-1)  
    _POSIX2_PBS_CHECKPOINT                  =(-1)  
    _POSIX2_PBS_LOCATE                      =(-1)  
    _POSIX2_PBS_MESSAGE                     =(-1)  
    _POSIX2_PBS_TRACK                       =(-1)  
    _POSIX2_SW_DEV                          =200112L  
    _POSIX2_UPE                             =200112L  
    _POSIX2_VERSION                         =200112L  
    _V6_ILP32_OFF32                         =(-1)  
    _V6_ILP32_OFFBIG                        =(1)  
    _V6_LP64_OFF64                          =(1)  
    _V6_LPBIG_OFFBIG                        =(1)  
    _XBS5_ILP32_OFF32                       =(-1)  
    _XBS5_ILP32_OFFBIG                      =(1)  
    _XBS5_LP64_OFF64                        =(1)  
    _XBS5_LPBIG_OFFBIG                      =(1)  
    _XOPEN_CRYPT                            =(1)  
    _XOPEN_ENH_I18N                         =(1)  
    _XOPEN_LEGACY                           =(-1)  
    _XOPEN_REALTIME                         =(-1)  
    _XOPEN_REALTIME_THREADS                 =(-1)  
    _XOPEN_SHM                              =(1)  
    _XOPEN_STREAMS                          =(-1)  
    _XOPEN_UNIX                             =(1)  
    _XOPEN_VERSION                          =600  
  
  
*********************************************************************  
  
Boost version 107100  
    BOOST_USER_CONFIG                       =<boost/config/user.hpp>  
    BOOST_COMPILER_CONFIG                   ="boost/config/compiler/clang.hpp"  
    BOOST_STDLIB_CONFIG                     ="boost/config/stdlib/libcpp.hpp"  
    BOOST_PLATFORM_CONFIG                   ="boost/config/platform/macos.hpp"  
    BOOST_HAS_THREADS                        [no value]  
    BOOST_DEDUCED_TYPENAME                  =typename  
    BOOST_HAS_DIRENT_H                       [no value]  
    BOOST_HAS_EXPM1                          [no value]  
    BOOST_HAS_GETTIMEOFDAY                   [no value]  
    BOOST_HAS_INT128                         [no value]  
    BOOST_HAS_LOG1P                          [no value]  
    BOOST_HAS_LONG_LONG                      [no value]  
    BOOST_HAS_NANOSLEEP                      [no value]  
    BOOST_HAS_NL_TYPES_H                     [no value]  
    BOOST_HAS_NRVO                           [no value]  
    BOOST_HAS_PARTIAL_STD_ALLOCATOR          [no value]  
    BOOST_HAS_PTHREADS                       [no value]  
    BOOST_HAS_PTHREAD_MUTEXATTR_SETTYPE      [no value]  
    BOOST_HAS_SCHED_YIELD                    [no value]  
    BOOST_HAS_SIGACTION                      [no value]  
    BOOST_HAS_STDINT_H                       [no value]  
    BOOST_HAS_UNISTD_H                       [no value]  
    BOOST_MSVC6_MEMBER_TEMPLATES             [no value]  
    BOOST_NO_CXX11_ADDRESSOF                 [no value]  
    BOOST_NO_CXX11_ALIGNAS                   [no value]  
    BOOST_NO_CXX11_ALLOCATOR                 [no value]  
    BOOST_NO_CXX11_ATOMIC_SMART_PTR          [no value]  
    BOOST_NO_CXX11_AUTO_DECLARATIONS         [no value]  
    BOOST_NO_CXX11_AUTO_MULTIDECLARATIONS    [no value]  
    BOOST_NO_CXX11_CHAR16_T                  [no value]  
    BOOST_NO_CXX11_CHAR32_T                  [no value]  
    BOOST_NO_CXX11_CONSTEXPR                 [no value]  
    BOOST_NO_CXX11_DECLTYPE                  [no value]  
    BOOST_NO_CXX11_DECLTYPE_N3276            [no value]  
    BOOST_NO_CXX11_DEFAULTED_FUNCTIONS       [no value]  
    BOOST_NO_CXX11_DEFAULTED_MOVES           [no value]  
    BOOST_NO_CXX11_DELETED_FUNCTIONS         [no value]  
    BOOST_NO_CXX11_EXPLICIT_CONVERSION_OPERATORS [no value]  
    BOOST_NO_CXX11_FINAL                     [no value]  
    BOOST_NO_CXX11_FIXED_LENGTH_VARIADIC_TEMPLATE_EXPANSION_PACKS [no value]  
    BOOST_NO_CXX11_FUNCTION_TEMPLATE_DEFAULT_ARGS [no value]  
    BOOST_NO_CXX11_HDR_ATOMIC                [no value]  
    BOOST_NO_CXX11_HDR_CHRONO                [no value]  
    BOOST_NO_CXX11_HDR_CODECVT               [no value]  
    BOOST_NO_CXX11_HDR_CONDITION_VARIABLE    [no value]  
    BOOST_NO_CXX11_HDR_FUNCTIONAL            [no value]  
    BOOST_NO_CXX11_HDR_FUTURE                [no value]  
    BOOST_NO_CXX11_HDR_INITIALIZER_LIST      [no value]  
    BOOST_NO_CXX11_HDR_MUTEX                 [no value]  
    BOOST_NO_CXX11_HDR_RANDOM                [no value]  
    BOOST_NO_CXX11_HDR_RATIO                 [no value]  
    BOOST_NO_CXX11_HDR_REGEX                 [no value]  
    BOOST_NO_CXX11_HDR_SYSTEM_ERROR          [no value]  
    BOOST_NO_CXX11_HDR_THREAD                [no value]  
    BOOST_NO_CXX11_HDR_TUPLE                 [no value]  
    BOOST_NO_CXX11_HDR_TYPEINDEX             [no value]  
    BOOST_NO_CXX11_HDR_TYPE_TRAITS           [no value]  
    BOOST_NO_CXX11_HDR_UNORDERED_MAP         [no value]  
    BOOST_NO_CXX11_HDR_UNORDERED_SET         [no value]  
    BOOST_NO_CXX11_INLINE_NAMESPACES         [no value]  
    BOOST_NO_CXX11_LAMBDAS                   [no value]  
    BOOST_NO_CXX11_LOCAL_CLASS_TEMPLATE_PARAMETERS [no value]  
    BOOST_NO_CXX11_NOEXCEPT                  [no value]  
    BOOST_NO_CXX11_NON_PUBLIC_DEFAULTED_FUNCTIONS [no value]  
    BOOST_NO_CXX11_NULLPTR                   [no value]  
    BOOST_NO_CXX11_NUMERIC_LIMITS            [no value]  
    BOOST_NO_CXX11_POINTER_TRAITS            [no value]  
    BOOST_NO_CXX11_RANGE_BASED_FOR           [no value]  
    BOOST_NO_CXX11_RAW_LITERALS              [no value]  
    BOOST_NO_CXX11_REF_QUALIFIERS            [no value]  
    BOOST_NO_CXX11_RVALUE_REFERENCES         [no value]  
    BOOST_NO_CXX11_SCOPED_ENUMS              [no value]  
    BOOST_NO_CXX11_SFINAE_EXPR               [no value]  
    BOOST_NO_CXX11_SMART_PTR                 [no value]  
    BOOST_NO_CXX11_STATIC_ASSERT             [no value]  
    BOOST_NO_CXX11_STD_ALIGN                 [no value]  
    BOOST_NO_CXX11_TEMPLATE_ALIASES          [no value]  
    BOOST_NO_CXX11_THREAD_LOCAL              [no value]  
    BOOST_NO_CXX11_TRAILING_RESULT_TYPES     [no value]  
    BOOST_NO_CXX11_UNICODE_LITERALS          [no value]  
    BOOST_NO_CXX11_UNIFIED_INITIALIZATION_SYNTAX [no value]  
    BOOST_NO_CXX11_USER_DEFINED_LITERALS     [no value]  
    BOOST_NO_CXX11_VARIADIC_TEMPLATES        [no value]  
    BOOST_NO_CXX14_AGGREGATE_NSDMI           [no value]  
    BOOST_NO_CXX14_CONSTEXPR                 [no value]  
    BOOST_NO_CXX14_DECLTYPE_AUTO             [no value]  
    BOOST_NO_CXX14_DIGIT_SEPARATORS          [no value]  
    BOOST_NO_CXX14_GENERIC_LAMBDAS           [no value]  
    BOOST_NO_CXX14_HDR_SHARED_MUTEX          [no value]  
    BOOST_NO_CXX14_INITIALIZED_LAMBDA_CAPTURES [no value]  
    BOOST_NO_CXX14_RETURN_TYPE_DEDUCTION     [no value]  
    BOOST_NO_CXX14_STD_EXCHANGE              [no value]  
    BOOST_NO_CXX14_VARIABLE_TEMPLATES        [no value]  
    BOOST_NO_CXX17_FOLD_EXPRESSIONS          [no value]  
    BOOST_NO_CXX17_HDR_OPTIONAL              [no value]  
    BOOST_NO_CXX17_HDR_STRING_VIEW           [no value]  
    BOOST_NO_CXX17_HDR_VARIANT               [no value]  
    BOOST_NO_CXX17_IF_CONSTEXPR              [no value]  
    BOOST_NO_CXX17_INLINE_VARIABLES          [no value]  
    BOOST_NO_CXX17_ITERATOR_TRAITS           [no value]  
    BOOST_NO_CXX17_STD_APPLY                 [no value]  
    BOOST_NO_CXX17_STD_INVOKE                [no value]  
    BOOST_NO_CXX17_STRUCTURED_BINDINGS       [no value]  
    BOOST_NO_MS_INT64_NUMERIC_LIMITS         [no value]  
    BOOST_STD_EXTENSION_NAMESPACE           =std  
    BOOST_UNREACHABLE_RETURN(0)             =__builtin_unreachable();  
    BOOST_CONSTEXPR                          [no value]  
    BOOST_CONSTEXPR_OR_CONST                =const  
    BOOST_STATIC_CONSTEXPR                  =static const  
    BOOST_NOEXCEPT                           [no value]  
    BOOST_FORCEINLINE                       =inline __attribute__ ((__always_inline__))  
    BOOST_NOINLINE                          =__attribute__ ((__noinline__))  
    BOOST_FALLTHROUGH                       =((void)0)  
    BOOST_MAY_ALIAS                         =__attribute__((__may_alias__))  
    BOOST_ATTRIBUTE_NO_UNIQUE_ADDRESS        [no value]  
    BOOST_ATTRIBUTE_UNUSED                  =__attribute__((__unused__))  
    BOOST_NORETURN                          =__attribute__ ((__noreturn__))  
  
  
*********************************************************************  
  
    __cpp_threadsafe_static_init            =200806L  
    __cpp_rtti                              =199711L  
    __cpp_exceptions                        =199711L  
config/test (develop)$ Clang version 11.0.0 (clang-1100.0.33.17)  
    __cplusplus                             =199711L  
    __STDC__                                =1  
    __GNUC__                                =4  
    __GNUC_MINOR__                          =2  
    __GNUC_PATCHLEVEL__                     =1  
    __GNUG__                                =4  
    __VERSION__                             ="4.2.1 Compatible Apple LLVM 11.0.0 (clang-1100.0.33.17)"  
    __REGISTER_PREFIX__                      [no value]  
    __USER_LABEL_PREFIX__                   =_  
    __EXCEPTIONS                            =1  
    __GXX_RTTI                              =1  
    __amd64__                               =1  
    __LP64__                                =1  
    __MACH__                                =1  
    __APPLE__                               =1  
    __APPLE_CC__                            =6000  
    __clang__                               =1  
    __clang_major__                         =11  
    __clang_minor__                         =0  
    __clang_version__                       ="11.0.0 (clang-1100.0.33.17)"  
    __clang_patchlevel__                    =0  
    __STDC_HOSTED__                         =1  
  
  
*********************************************************************  
  
libc++ version 8000  
    _LIBCPP_VERSION                         =8000  
  
  
*********************************************************************  
  
Detected Platform: Mac OS  
    Type char is signed  
    Type wchar_t is signed  
    byte order for type short               =0 8   
    byte order for type int                 =0 8 16 24   
    byte order for type long                =0 8 16 24 32 40 48 56   
    sizeof(wchar_t)                         =4  
    sizeof(short)                           =2  
    sizeof(int)                             =4  
    sizeof(long)                            =8  
    sizeof(size_t)                          =8  
    sizeof(ptrdiff_t)                       =8  
    sizeof(void*)                           =8  
    sizeof(void(*)(void))                   =8  
    sizeof(float)                           =4  
    sizeof(double)                          =8  
    sizeof(long double)                     =16  
    CHAR_BIT                                =8  
    CHAR_MAX                                =127  
    WCHAR_MAX                               =2147483647  
    SHRT_MAX                                =32767  
    INT_MAX                                 =2147483647  
    LONG_MAX                                =9223372036854775807L  
    LLONG_MAX                               =0x7fffffffffffffffLL  
    LONG_LONG_MAX                           =9223372036854775807LL  
    ULLONG_MAX                              =0xffffffffffffffffULL  
    ULONG_LONG_MAX                          =(9223372036854775807LL*2ULL+1ULL)  
    _POSIX_ADVISORY_INFO                    =(-1)  
    _POSIX_ASYNCHRONOUS_IO                  =(-1)  
    _POSIX_BARRIERS                         =(-1)  
    _POSIX_CHOWN_RESTRICTED                 =200112L  
    _POSIX_CLOCK_SELECTION                  =(-1)  
    _POSIX_CPUTIME                          =(-1)  
    _POSIX_FSYNC                            =200112L  
    _POSIX_JOB_CONTROL                      =200112L  
    _POSIX_MAPPED_FILES                     =200112L  
    _POSIX_MEMLOCK                          =(-1)  
    _POSIX_MEMLOCK_RANGE                    =(-1)  
    _POSIX_MEMORY_PROTECTION                =200112L  
    _POSIX_MESSAGE_PASSING                  =(-1)  
    _POSIX_MONOTONIC_CLOCK                  =(-1)  
    _POSIX_NO_TRUNC                         =200112L  
    _POSIX_PRIORITIZED_IO                   =(-1)  
    _POSIX_PRIORITY_SCHEDULING              =(-1)  
    _POSIX_RAW_SOCKETS                      =(-1)  
    _POSIX_READER_WRITER_LOCKS              =200112L  
    _POSIX_REALTIME_SIGNALS                 =(-1)  
    _POSIX_REGEXP                           =200112L  
    _POSIX_SAVED_IDS                        =200112L  
    _POSIX_SEMAPHORES                       =(-1)  
    _POSIX_SHARED_MEMORY_OBJECTS            =(-1)  
    _POSIX_SHELL                            =200112L  
    _POSIX_SPAWN                            =(-1)  
    _POSIX_SPIN_LOCKS                       =(-1)  
    _POSIX_SPORADIC_SERVER                  =(-1)  
    _POSIX_SYNCHRONIZED_IO                  =(-1)  
    _POSIX_THREAD_ATTR_STACKADDR            =200112L  
    _POSIX_THREAD_ATTR_STACKSIZE            =200112L  
    _POSIX_THREAD_CPUTIME                   =(-1)  
    _POSIX_THREAD_PRIO_INHERIT              =(-1)  
    _POSIX_THREAD_PRIO_PROTECT              =(-1)  
    _POSIX_THREAD_PRIORITY_SCHEDULING       =(-1)  
    _POSIX_THREAD_PROCESS_SHARED            =200112L  
    _POSIX_THREAD_SAFE_FUNCTIONS            =200112L  
    _POSIX_THREAD_SPORADIC_SERVER           =(-1)  
    _POSIX_THREADS                          =200112L  
    _POSIX_TIMEOUTS                         =(-1)  
    _POSIX_TIMERS                           =(-1)  
    _POSIX_TRACE                            =(-1)  
    _POSIX_TRACE_EVENT_FILTER               =(-1)  
    _POSIX_TRACE_INHERIT                    =(-1)  
    _POSIX_TRACE_LOG                        =(-1)  
    _POSIX_TYPED_MEMORY_OBJECTS             =(-1)  
    _POSIX_VDISABLE                         =((unsigned char)'\377')  
    _POSIX_VERSION                          =200112L  
    _POSIX2_C_BIND                          =200112L  
    _POSIX2_C_DEV                           =200112L  
    _POSIX2_CHAR_TERM                       =200112L  
    _POSIX2_FORT_DEV                        =(-1)  
    _POSIX2_FORT_RUN                        =200112L  
    _POSIX2_LOCALEDEF                       =200112L  
    _POSIX2_PBS                             =(-1)  
    _POSIX2_PBS_ACCOUNTING                  =(-1)  
    _POSIX2_PBS_CHECKPOINT                  =(-1)  
    _POSIX2_PBS_LOCATE                      =(-1)  
    _POSIX2_PBS_MESSAGE                     =(-1)  
    _POSIX2_PBS_TRACK                       =(-1)  
    _POSIX2_SW_DEV                          =200112L  
    _POSIX2_UPE                             =200112L  
    _POSIX2_VERSION                         =200112L  
    _V6_ILP32_OFF32                         =(-1)  
    _V6_ILP32_OFFBIG                        =(1)  
    _V6_LP64_OFF64                          =(1)  
    _V6_LPBIG_OFFBIG                        =(1)  
    _XBS5_ILP32_OFF32                       =(-1)  
    _XBS5_ILP32_OFFBIG                      =(1)  
    _XBS5_LP64_OFF64                        =(1)  
    _XBS5_LPBIG_OFFBIG                      =(1)  
    _XOPEN_CRYPT                            =(1)  
    _XOPEN_ENH_I18N                         =(1)  
    _XOPEN_LEGACY                           =(-1)  
    _XOPEN_REALTIME                         =(-1)  
    _XOPEN_REALTIME_THREADS                 =(-1)  
    _XOPEN_SHM                              =(1)  
    _XOPEN_STREAMS                          =(-1)  
    _XOPEN_UNIX                             =(1)  
    _XOPEN_VERSION                          =600  
  
  
*********************************************************************  
  
Boost version 107300  
    BOOST_USER_CONFIG                       =<boost/config/user.hpp>  
    BOOST_COMPILER_CONFIG                   ="boost/config/compiler/clang.hpp"  
    BOOST_STDLIB_CONFIG                     ="boost/config/stdlib/libcpp.hpp"  
    BOOST_PLATFORM_CONFIG                   ="boost/config/platform/macos.hpp"  
    BOOST_HAS_THREADS                        [no value]  
    BOOST_DEDUCED_TYPENAME                  =typename  
    BOOST_HAS_DIRENT_H                       [no value]  
    BOOST_HAS_EXPM1                          [no value]  
    BOOST_HAS_GETTIMEOFDAY                   [no value]  
    BOOST_HAS_INT128                         [no value]  
    BOOST_HAS_LOG1P                          [no value]  
    BOOST_HAS_LONG_LONG                      [no value]  
    BOOST_HAS_NANOSLEEP                      [no value]  
    BOOST_HAS_NL_TYPES_H                     [no value]  
    BOOST_HAS_NRVO                           [no value]  
    BOOST_HAS_PARTIAL_STD_ALLOCATOR          [no value]  
    BOOST_HAS_PTHREADS                       [no value]  
    BOOST_HAS_PTHREAD_MUTEXATTR_SETTYPE      [no value]  
    BOOST_HAS_SCHED_YIELD                    [no value]  
    BOOST_HAS_SIGACTION                      [no value]  
    BOOST_HAS_STDINT_H                       [no value]  
    BOOST_HAS_UNISTD_H                       [no value]  
    BOOST_MSVC6_MEMBER_TEMPLATES             [no value]  
    BOOST_NO_CXX11_ADDRESSOF                 [no value]  
    BOOST_NO_CXX11_ALIGNAS                   [no value]  
    BOOST_NO_CXX11_ALLOCATOR                 [no value]  
    BOOST_NO_CXX11_ATOMIC_SMART_PTR          [no value]  
    BOOST_NO_CXX11_AUTO_DECLARATIONS         [no value]  
    BOOST_NO_CXX11_AUTO_MULTIDECLARATIONS    [no value]  
    BOOST_NO_CXX11_CHAR16_T                  [no value]  
    BOOST_NO_CXX11_CHAR32_T                  [no value]  
    BOOST_NO_CXX11_CONSTEXPR                 [no value]  
    BOOST_NO_CXX11_DECLTYPE                  [no value]  
    BOOST_NO_CXX11_DECLTYPE_N3276            [no value]  
    BOOST_NO_CXX11_DEFAULTED_FUNCTIONS       [no value]  
    BOOST_NO_CXX11_DEFAULTED_MOVES           [no value]  
    BOOST_NO_CXX11_DELETED_FUNCTIONS         [no value]  
    BOOST_NO_CXX11_EXPLICIT_CONVERSION_OPERATORS [no value]  
    BOOST_NO_CXX11_FINAL                     [no value]  
    BOOST_NO_CXX11_FIXED_LENGTH_VARIADIC_TEMPLATE_EXPANSION_PACKS [no value]  
    BOOST_NO_CXX11_FUNCTION_TEMPLATE_DEFAULT_ARGS [no value]  
    BOOST_NO_CXX11_HDR_ATOMIC                [no value]  
    BOOST_NO_CXX11_HDR_CHRONO                [no value]  
    BOOST_NO_CXX11_HDR_CODECVT               [no value]  
    BOOST_NO_CXX11_HDR_CONDITION_VARIABLE    [no value]  
    BOOST_NO_CXX11_HDR_EXCEPTION             [no value]  
    BOOST_NO_CXX11_HDR_FUNCTIONAL            [no value]  
    BOOST_NO_CXX11_HDR_FUTURE                [no value]  
    BOOST_NO_CXX11_HDR_INITIALIZER_LIST      [no value]  
    BOOST_NO_CXX11_HDR_MUTEX                 [no value]  
    BOOST_NO_CXX11_HDR_RANDOM                [no value]  
    BOOST_NO_CXX11_HDR_RATIO                 [no value]  
    BOOST_NO_CXX11_HDR_REGEX                 [no value]  
    BOOST_NO_CXX11_HDR_SYSTEM_ERROR          [no value]  
    BOOST_NO_CXX11_HDR_THREAD                [no value]  
    BOOST_NO_CXX11_HDR_TUPLE                 [no value]  
    BOOST_NO_CXX11_HDR_TYPEINDEX             [no value]  
    BOOST_NO_CXX11_HDR_TYPE_TRAITS           [no value]  
    BOOST_NO_CXX11_HDR_UNORDERED_MAP         [no value]  
    BOOST_NO_CXX11_HDR_UNORDERED_SET         [no value]  
    BOOST_NO_CXX11_INLINE_NAMESPACES         [no value]  
    BOOST_NO_CXX11_LAMBDAS                   [no value]  
    BOOST_NO_CXX11_LOCAL_CLASS_TEMPLATE_PARAMETERS [no value]  
    BOOST_NO_CXX11_NOEXCEPT                  [no value]  
    BOOST_NO_CXX11_NON_PUBLIC_DEFAULTED_FUNCTIONS [no value]  
    BOOST_NO_CXX11_NULLPTR                   [no value]  
    BOOST_NO_CXX11_NUMERIC_LIMITS            [no value]  
    BOOST_NO_CXX11_POINTER_TRAITS            [no value]  
    BOOST_NO_CXX11_RANGE_BASED_FOR           [no value]  
    BOOST_NO_CXX11_RAW_LITERALS              [no value]  
    BOOST_NO_CXX11_REF_QUALIFIERS            [no value]  
    BOOST_NO_CXX11_RVALUE_REFERENCES         [no value]  
    BOOST_NO_CXX11_SCOPED_ENUMS              [no value]  
    BOOST_NO_CXX11_SFINAE_EXPR               [no value]  
    BOOST_NO_CXX11_SMART_PTR                 [no value]  
    BOOST_NO_CXX11_STATIC_ASSERT             [no value]  
    BOOST_NO_CXX11_STD_ALIGN                 [no value]  
    BOOST_NO_CXX11_TEMPLATE_ALIASES          [no value]  
    BOOST_NO_CXX11_THREAD_LOCAL              [no value]  
    BOOST_NO_CXX11_TRAILING_RESULT_TYPES     [no value]  
    BOOST_NO_CXX11_UNICODE_LITERALS          [no value]  
    BOOST_NO_CXX11_UNIFIED_INITIALIZATION_SYNTAX [no value]  
    BOOST_NO_CXX11_USER_DEFINED_LITERALS     [no value]  
    BOOST_NO_CXX11_VARIADIC_TEMPLATES        [no value]  
    BOOST_NO_CXX14_AGGREGATE_NSDMI           [no value]  
    BOOST_NO_CXX14_CONSTEXPR                 [no value]  
    BOOST_NO_CXX14_DECLTYPE_AUTO             [no value]  
    BOOST_NO_CXX14_DIGIT_SEPARATORS          [no value]  
    BOOST_NO_CXX14_GENERIC_LAMBDAS           [no value]  
    BOOST_NO_CXX14_HDR_SHARED_MUTEX          [no value]  
    BOOST_NO_CXX14_INITIALIZED_LAMBDA_CAPTURES [no value]  
    BOOST_NO_CXX14_RETURN_TYPE_DEDUCTION     [no value]  
    BOOST_NO_CXX14_STD_EXCHANGE              [no value]  
    BOOST_NO_CXX14_VARIABLE_TEMPLATES        [no value]  
    BOOST_NO_CXX17_FOLD_EXPRESSIONS          [no value]  
    BOOST_NO_CXX17_HDR_OPTIONAL              [no value]  
    BOOST_NO_CXX17_HDR_STRING_VIEW           [no value]  
    BOOST_NO_CXX17_HDR_VARIANT               [no value]  
    BOOST_NO_CXX17_IF_CONSTEXPR              [no value]  
    BOOST_NO_CXX17_INLINE_VARIABLES          [no value]  
    BOOST_NO_CXX17_ITERATOR_TRAITS           [no value]  
    BOOST_NO_CXX17_STD_APPLY                 [no value]  
    BOOST_NO_CXX17_STD_INVOKE                [no value]  
    BOOST_NO_CXX17_STRUCTURED_BINDINGS       [no value]  
    BOOST_NO_MS_INT64_NUMERIC_LIMITS         [no value]  
    BOOST_STD_EXTENSION_NAMESPACE           =std  
    BOOST_UNREACHABLE_RETURN(0)             =__builtin_unreachable();  
    BOOST_CONSTEXPR                          [no value]  
    BOOST_CONSTEXPR_OR_CONST                =const  
    BOOST_STATIC_CONSTEXPR                  =static const  
    BOOST_NOEXCEPT                           [no value]  
    BOOST_FORCEINLINE                       =inline __attribute__ ((__always_inline__))  
    BOOST_NOINLINE                          =__attribute__ ((__noinline__))  
    BOOST_FALLTHROUGH                       =((void)0)  
    BOOST_MAY_ALIAS                         =__attribute__((__may_alias__))  
    BOOST_ATTRIBUTE_NO_UNIQUE_ADDRESS        [no value]  
    BOOST_ATTRIBUTE_UNUSED                  =__attribute__((__unused__))  
    BOOST_NORETURN                          =__attribute__ ((__noreturn__))  
  
  
*********************************************************************  
  
    __cpp_threadsafe_static_init            =200806L  
    __cpp_rtti                              =199711L  
    __cpp_exceptions                        =199711L  
```

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-04-02 16:00:23 UTC  
> Url: https://github.com/boostorg/math/issues/334#issuecomment-607935472  

It's in C++03 mode, notice:  
  
__cplusplus                             =199711L  
  
And all the BOOST_NO_CXX11_* macros are set.

---

## Comment 6

> Username: NAThompson  
> Created at: 2020-04-03 14:47:28 UTC  
> Url: https://github.com/boostorg/math/issues/334#issuecomment-608474596  

I just reconfigured and ran  
  
```  
math/example$ ../../../b2 --reconfigure cxxflags="--std=c++11"  
```  
  
and did not encounter the warnings; my bad.
