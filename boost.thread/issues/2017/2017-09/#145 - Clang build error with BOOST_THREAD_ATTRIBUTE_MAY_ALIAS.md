# #145 - Clang build error with BOOST_THREAD_ATTRIBUTE_MAY_ALIAS [Closed]

> Username: egorpugin  
> Created at: 2017-09-13 11:59:53 UTC  
> Updated at: 2017-10-15 19:18:37 UTC  
> Closed at: 2017-09-19 22:21:30 UTC  
> Url: https://github.com/boostorg/thread/issues/145  

Clang output:  
```  
-c src/pthread/thread.cpp  
In file included from src/pthread/thread.cpp:17:  
In file included from include/boost/thread/once.hpp:20:  
include/boost/thread/pthread/once_atomic.hpp:91:5: error: unknown type name 'BOOST_THREAD_ATTRIBUTE_MAY_ALIAS'  
    BOOST_THREAD_ATTRIBUTE_MAY_ALIAS thread_detail::atomic_int_type storage;  
    ^  
include/boost/thread/pthread/once_atomic.hpp:91:53: error: non-friend class member 'atomic_int_type' cannot have a qualified name  
    BOOST_THREAD_ATTRIBUTE_MAY_ALIAS thread_detail::atomic_int_type storage;  
                                     ~~~~~~~~~~~~~~~^  
include/boost/thread/pthread/once_atomic.hpp:91:68: error: expected ';' at end of declaration list  
    BOOST_THREAD_ATTRIBUTE_MAY_ALIAS thread_detail::atomic_int_type storage;  
                                                                   ^  
include/boost/thread/pthread/once_atomic.hpp:100:52: error: no member named 'storage' in 'boost::once_flag'  
      return reinterpret_cast< atomic_type& >(flag.storage);  
                                              ~~~~ ^  
```  
  
See these lines https://github.com/boostorg/thread/blob/develop/include/boost/thread/detail/config.hpp#L20  
  
It's suspicious that in `#ifndef` we define macro to nothing and then instantly set it to `MAY_ALIAS`.  
Maybe there's missing `#else`?  
  
  
I have a custom build (b2 is not used), so it's possible that I could miss some flags set.

---

## Comment 1

> Username: viboes  
> Created at: 2017-09-13 19:00:12 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-329264751  

This has been introduced in https://github.com/boostorg/thread/commit/23e7135f2c5f81b0192700a2c5de82599916d3c5#diff-54af14da6bc000f779c91e3087132068  
  
Please, could you just try with  
```  
#define BOOST_THREAD_ATTRIBUTE_MAY_ALIAS BOOST_MAY_ALIAS  
```  
  
Andrey, what do you think?

---

## Comment 2

> Username: viboes  
> Created at: 2017-09-13 19:06:07 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-329266348  

`BOOST_THREAD_DETAIL_USE_ATTRIBUTE_MAY_ALIAS` is not used anymore.  
  
I suspect that BOOST_MAY_ALIAS is not defined on your configuration.  
  
Please, could you check if it is defined. If not could you try  
```  
#ifdef BOOST_MAY_ALIAS  
#define BOOST_THREAD_ATTRIBUTE_MAY_ALIAS BOOST_MAY_ALIAS  
#else  
#define BOOST_THREAD_ATTRIBUTE_MAY_ALIAS  
#endif  
```

---

## Comment 3

> Username: egorpugin  
> Created at: 2017-09-13 21:08:03 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-329297395  

I empty-defined BOOST_THREAD_ATTRIBUTE_MAY_ALIAS and it works for me. It's ok as a workaround. (Did not try your suggestion.)

---

## Comment 4

> Username: viboes  
> Created at: 2017-09-14 05:26:30 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-329376316  

Obviously, I can not define it as empty :)  
Please give a try to my last suggestion:  
```  
#ifdef BOOST_MAY_ALIAS  
#define BOOST_THREAD_ATTRIBUTE_MAY_ALIAS BOOST_MAY_ALIAS  
#else  
#define BOOST_THREAD_ATTRIBUTE_MAY_ALIAS  
#endif  
```

---

## Comment 5

> Username: egorpugin  
> Created at: 2017-09-14 05:57:18 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-329381561  

It works.

---

## Comment 6

> Username: viboes  
> Created at: 2017-09-14 06:08:40 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-329383169  

Great, I will fix it.  
  
Thanks for reporting and supporting the test.

---

## Comment 7

> Username: egorpugin  
> Created at: 2017-09-14 06:10:52 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-329383492  

Ok, thanks!

---

## Comment 8

> Username: viboes  
> Created at: 2017-09-14 06:16:34 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-329384351  

The issue comes from the fact that BOOST_MAY_ALIAS is not always defined  
  
```  
// Type aliasing hint.  
#if __has_attribute(__may_alias__)  
#  define BOOST_MAY_ALIAS __attribute__((__may_alias__))  
#endif  
```  
  
Wondering if  this should be  
  
```  
// Type aliasing hint.  
#if __has_attribute(__may_alias__)  
#  define BOOST_MAY_ALIAS __attribute__((__may_alias__))  
#else  
#  define BOOST_MAY_ALIAS  
#endif  
```  
  
but BOOST_MAY_ALIAS is not documented.  
  
I've created a Boost.Config ticket. https://svn.boost.org/trac10/ticket/13211

---

## Comment 9

> Username: viboes  
> Created at: 2017-09-14 17:28:00 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-329552703  

It is weird. Please could you confirm you have this code in file /boost/config/detail/sufix.hpp?  
  
```  
#if !defined(BOOST_MAY_ALIAS)  
#  define BOOST_NO_MAY_ALIAS  
#  define BOOST_MAY_ALIAS  
#endif  
```

---

## Comment 10

> Username: viboes  
> Created at: 2017-09-14 17:30:38 UTC  
> Updated at: 2017-09-14 17:33:04 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-329553424  

BTW, this was as it is since the 12 Jul.  
https://github.com/boostorg/config/commit/4a58e5360a81b502078733ab4bec2abe033af8c2#diff-57dd29863d0bc07f45bc221f60715f97  
  
Which version of boost are you using?

---

## Comment 11

> Username: egorpugin  
> Created at: 2017-09-14 17:36:34 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-329555044  

1.65.0

---

## Comment 12

> Username: viboes  
> Created at: 2017-09-18 16:23:56 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330276627  

I would need the output of the pre-processor, as I don't see what is wrong.

---

## Comment 13

> Username: egorpugin  
> Created at: 2017-09-18 19:17:35 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330327809  

In `include/boost/thread/detail/config.hpp#L20` you have now:  
```  
#if BOOST_MAY_ALIAS // == #if !!BOOST_MAY_ALIAS  
#define BOOST_THREAD_DETAIL_USE_ATTRIBUTE_MAY_ALIAS // define to nothing  
#endif  
#define BOOST_THREAD_DETAIL_USE_ATTRIBUTE_MAY_ALIAS BOOST_MAY_ALIAS // unconditional may alias  
```

---

## Comment 14

> Username: viboes  
> Created at: 2017-09-18 22:40:43 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330376394  

This is not what I see.  
```  
#if !defined(BOOST_NO_MAY_ALIAS)  
  
  // GCC since 3.3 and some other compilers have may_alias attribute that helps  
  // to alleviate optimizer issues with regard to violation of the strict aliasing rules.  
  
  #define BOOST_THREAD_DETAIL_USE_ATTRIBUTE_MAY_ALIAS  
#endif  
#define BOOST_THREAD_ATTRIBUTE_MAY_ALIAS BOOST_MAY_ALIAS  
```  
  
See https://github.com/boostorg/thread/blob/develop/include/boost/thread/detail/config.hpp#L20

---

## Comment 15

> Username: egorpugin  
> Created at: 2017-09-18 22:56:44 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330379237  

Yes, but after simplification? You have double negation and get `#ifdef BOOST_MAY_ALIAS`.  
Next, no matter what's inside of this ifdef, you always assign `BOOST_MAY_ALIAS` to `BOOST_THREAD_ATTRIBUTE_MAY_ALIAS`. Whole ifdef does not make sense.  
  
Correct me if I'm getting something wrong in this small piece of code (preprocessor).

---

## Comment 16

> Username: viboes  
> Created at: 2017-09-18 23:50:15 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330387808  

There are two symbols `BOOST_NO_MAY_ALIAS` and  `BOOST_MAY_ALIAS`. The last is always defined. The fist not.

---

## Comment 17

> Username: egorpugin  
> Created at: 2017-09-19 09:36:46 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330485352  

I see, thanks.

---

## Comment 18

> Username: viboes  
> Created at: 2017-09-19 19:18:03 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330642741  

So I don't understand where is the problem. Why is there a compile error?  
Could you add the command line and the preprocessed output (flag -E)?

---

## Comment 19

> Username: egorpugin  
> Created at: 2017-09-19 20:05:36 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330657702  

Sure.  
  
Full command and preprocessed output:  
```$ cd /Users/egor/.cppan/storage/obj/3b/8a/68a4/build/f7add531/cppan/3b8a68a4 && /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ -DBOOST_ALL_DYN_LINK -DBOOST_ATOMIC_DYN_LINK -DBOOST_ATOMIC_USE_DLL -DBOOST_CHRONO_DYN_LINK -DBOOST_CHRONO_USE_DLL -DBOOST_DATE_TIME_DYN_LINK -DBOOST_DATE_TIME_USE_DLL -DBOOST_SYSTEM_DYN_LINK -DBOOST_SYSTEM_USE_DLL -DBOOST_THREAD_BUILDING_THE_LIB -DBOOST_THREAD_BUILD_DLL -DBOOST_THREAD_DYN_LINK -DBOOST_THREAD_SOURCE -DBOOST_THREAD_USE_DLL -DCPPAN -DCPPAN_API_pvt_cppan_demo_boost_atomic_1_65_0=CPPAN_SYMBOL_IMPORT -DCPPAN_API_pvt_cppan_demo_boost_chrono_1_65_0=CPPAN_SYMBOL_IMPORT -DCPPAN_API_pvt_cppan_demo_boost_date_time_1_65_0=CPPAN_SYMBOL_IMPORT -DCPPAN_API_pvt_cppan_demo_boost_system_1_65_0=CPPAN_SYMBOL_IMPORT -DCPPAN_API_pvt_cppan_demo_boost_thread_1_65_0=CPPAN_SYMBOL_EXPORT -DCPPAN_BUILD -DCPPAN_CONFIG=\"91a9a0e5\" -DCPPAN_SYMBOL_EXPORT="" -DCPPAN_SYMBOL_IMPORT="" -DHAVE_SIZE_T=1 -DHAVE_VOID_P=1 -DPACKAGE=\"pvt.cppan.demo.boost.thread\" -DPACKAGE_BUGREPORT=\"\" -DPACKAGE_BUILD_CONFIG=\"Release\" -DPACKAGE_COPYRIGHT_YEAR=2017 -DPACKAGE_NAME=\"pvt.cppan.demo.boost.thread\" -DPACKAGE_NAME_LAST=\"thread\" -DPACKAGE_STRING=\"3b8a68a4\" -DPACKAGE_URL=\"\" -DPACKAGE_VERSION=\"1.65.0\" -DSIZEOF_SIZE_T=8 -DSIZEOF_VOID_P=8 -DSIZE_OF_SIZE_T=8 -DSIZE_OF_VOID_P=8 -D_3b8a68a4_EXPORTS @CMakeFiles/3b8a68a4.dir/includes_CXX.rsp -O3 -DNDEBUG -fPIC   -Wno-macro-redefined -c /Users/egor/.cppan/storage/src/3b/8a/68a4/src/pthread/thread.cpp -E > thread.pp```  
  
  
[thread.pp.zip](https://github.com/boostorg/thread/files/1315682/thread.pp.zip)

---

## Comment 20

> Username: viboes  
> Created at: 2017-09-19 20:17:26 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330660800  

It is weird. We have  
  
```  
    BOOST_MAY_ALIAS thread_detail::atomic_int_type storage;  
```  
  
When we have   
  
```  
#if !defined(BOOST_MAY_ALIAS)  
#  define BOOST_NO_MAY_ALIAS  
#  define BOOST_MAY_ALIAS  
#endif  
// ....  
#define BOOST_THREAD_ATTRIBUTE_MAY_ALIAS BOOST_MAY_ALIAS  
```  
  
How is it that `BOOST_MAY_ALIAS`has not been replaced?

---

## Comment 21

> Username: viboes  
> Created at: 2017-09-19 20:18:42 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330661126  

Anyway, I'll commit  
  
```  
#ifdef BOOST_MAY_ALIAS  
#define BOOST_THREAD_ATTRIBUTE_MAY_ALIAS BOOST_MAY_ALIAS  
#else  
#define BOOST_THREAD_ATTRIBUTE_MAY_ALIAS  
#endif  
```  
  
As it works for you.

---

## Comment 22

> Username: viboes  
> Created at: 2017-09-19 21:32:00 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330679809  

https://github.com/boostorg/thread/commit/1c51b5d9b8d24852ad208ab9e3e134fdce7c0d97

---

## Comment 23

> Username: egorpugin  
> Created at: 2017-09-19 22:21:30 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330690570  

Thanks!  
Should I close?

---

## Comment 24

> Username: viboes  
> Created at: 2017-09-20 17:06:38 UTC  
> Url: https://github.com/boostorg/thread/issues/145#issuecomment-330917462  

No problem, I prefer to close once I have do the merge to master.
