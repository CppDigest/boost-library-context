# #238 - ICU detection broken with ICU 75.1 and GCC 8.4 [Closed]

> Username: brad0  
> Created at: 2024-08-26 22:46:31 UTC  
> Updated at: 2024-08-28 04:34:50 UTC  
> Closed at: 2024-08-28 04:34:50 UTC  
> Url: https://github.com/boostorg/locale/issues/238  

Boost 1.84.  
  
After updating to ICU 75.1 we noticed that Boost was failing to build on archs utilizing GCC 8.4. The ICU test is now failing.  
  
ICU 75.1 requires C++17 but GCC 8.4 defaults to C++14.  
  
```  
gcc.compile.c++ bin.v2/libs/regex/build/gcc-8/release/link-static/pch-off/threading-multi/visibility-hidden/has_icu_test.o  
  
    "c++"   -fvisibility-inlines-hidden -m64 -pthread -finline-functions -Wno-inline -Wall -fvisibility=hidden -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_HAS_ICU=1 -DNDEBUG   -I"." -I"/usr/local/include"  -c -o "bin.v2/libs/regex/build/gcc-8/release/link-static/pch-off/threading-multi/visibility-hidden/has_icu_test.o" "libs/regex/build/has_icu_test.cpp"  
  
In file included from /usr/local/include/unicode/uset.h:36,  
                 from /usr/local/include/unicode/unorm2.h:35,  
                 from /usr/local/include/unicode/unorm.h:25,  
                 from /usr/local/include/unicode/ucol.h:17,  
                 from /usr/local/include/unicode/coll.h:62,  
                 from libs/regex/build/has_icu_test.cpp:15:  
/usr/local/include/unicode/localpointer.h:561:31: error: 'auto' parameter not permitted in this context  
 template <typename Type, auto closeFunction>  
                               ^~~~~~~~~~~~~  
/usr/local/include/unicode/localpointer.h:573:76: error: template argument 2 is invalid  
     explicit LocalOpenPointer(std::unique_ptr<Type, decltype(closeFunction)> &&p)  
                                                                            ^  
/usr/local/include/unicode/localpointer.h:583:78: error: template argument 2 is invalid  
     LocalOpenPointer &operator=(std::unique_ptr<Type, decltype(closeFunction)> &&p) {  
                                                                              ^  
/usr/local/include/unicode/localpointer.h:599:59: error: template argument 2 is invalid  
     operator std::unique_ptr<Type, decltype(closeFunction)> () && {  
                                                           ^  
/usr/local/include/unicode/localpointer.h:551:81: note: invalid template non-type parameter  
     using LocalPointerClassName = internal::LocalOpenPointer<Type, closeFunction>  
                                                                                 ^  
/usr/local/include/unicode/uset.h:358:1: note: in expansion of macro 'U_DEFINE_LOCAL_OPEN_POINTER'  
 U_DEFINE_LOCAL_OPEN_POINTER(LocalUSetPointer, USet, uset_close);  
 ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/unicode/localpointer.h:551:81: note: invalid template non-type parameter  
     using LocalPointerClassName = internal::LocalOpenPointer<Type, closeFunction>  
                                                                                 ^  
/usr/local/include/unicode/unorm2.h:286:1: note: in expansion of macro 'U_DEFINE_LOCAL_OPEN_POINTER'  
 U_DEFINE_LOCAL_OPEN_POINTER(LocalUNormalizer2Pointer, UNormalizer2, unorm2_close);  
 ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/unicode/localpointer.h:551:81: note: invalid template non-type parameter  
     using LocalPointerClassName = internal::LocalOpenPointer<Type, closeFunction>  
                                                                                 ^  
/usr/local/include/unicode/uenum.h:69:1: note: in expansion of macro 'U_DEFINE_LOCAL_OPEN_POINTER'  
 U_DEFINE_LOCAL_OPEN_POINTER(LocalUEnumerationPointer, UEnumeration, uenum_close);  
 ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/unicode/localpointer.h:551:81: note: invalid template non-type parameter  
     using LocalPointerClassName = internal::LocalOpenPointer<Type, closeFunction>  
                                                                                 ^  
/usr/local/include/unicode/ucol.h:553:1: note: in expansion of macro 'U_DEFINE_LOCAL_OPEN_POINTER'  
 U_DEFINE_LOCAL_OPEN_POINTER(LocalUCollatorPointer, UCollator, ucol_close);  
 ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/unicode/unistr.h:39,  
                 from /usr/local/include/unicode/strenum.h:20,  
                 from /usr/local/include/unicode/locid.h:40,  
                 from /usr/local/include/unicode/coll.h:64,  
                 from libs/regex/build/has_icu_test.cpp:15:  
/usr/local/include/unicode/stringpiece.h:134:23: error: 'is_same_v' is not a member of 'std'  
                 (std::is_same_v<decltype(T().data()), const char*>  
                       ^~~~~~~~~  
/usr/local/include/unicode/stringpiece.h:134:23: note: suggested alternative: 'is_same'  
                 (std::is_same_v<decltype(T().data()), const char*>  
                       ^~~~~~~~~  
                       is_same  
/usr/local/include/unicode/stringpiece.h:134:66: error: template argument 1 is invalid  
                 (std::is_same_v<decltype(T().data()), const char*>  
                                                                  ^  
/usr/local/include/unicode/stringpiece.h:138:17: error: expected '>' before ')' token  
                 ) &&  
                 ^  
/usr/local/include/unicode/stringpiece.h:138:17: error: expected unqualified-id before ')' token  
...skipped <pbin.v2/libs/regex/build/gcc-8/release/link-static/pch-off/threading-multi/visibility-hidden>has_icu for lack of <pbin.v2/libs/regex/build/gcc-8/release/link-static/pch-off/threading-multi/visibility-hidden>has_icu_test.o...  
...failed updating 1 target...  
```

---

## Comment 1

> Username: Flamefire  
> Created at: 2024-08-27 14:08:17 UTC  
> Url: https://github.com/boostorg/locale/issues/238#issuecomment-2312676744  

> ICU 75.1 requires C++17 but GCC 8.4 defaults to C++14.  
  
So this is the issue then. You hence need to compile Boost with C++17 as otherwise ICU is not usable and hence (correctly) not detected as available.  
  
As you are using b2 you can add `cxxstd=17` to the invocation of b2.  
  
Hope that helps!

---

## Comment 2

> Username: brad0  
> Created at: 2024-08-28 04:34:48 UTC  
> Url: https://github.com/boostorg/locale/issues/238#issuecomment-2314238368  

> > ICU 75.1 requires C++17 but GCC 8.4 defaults to C++14.  
>   
> So this is the issue then. You hence need to compile Boost with C++17 as otherwise ICU is not usable and hence (correctly) not detected as available.  
>   
> As you are using b2 you can add `cxxstd=17` to the invocation of b2.  
>   
> Hope that helps!  
  
Ya, looking back at our commit history it looks like we've done this before many years ago.  
  
I'll do the same but with C++17.  
  
Thanks.
