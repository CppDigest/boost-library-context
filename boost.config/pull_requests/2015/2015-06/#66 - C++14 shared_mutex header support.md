# #66 C++14 shared_mutex header support [Merged]

> Username: eldiener  
> Created at: 2015-06-28 01:53:07 UTC  
> Updated at: 2015-07-25 13:12:52 UTC  
> Merged at: 2015-07-07 10:18:21 UTC  
> Closed at: 2015-07-07 10:18:21 UTC  
> Url: https://github.com/boostorg/config/pull/66  

Support for C++14 shared_mutex header with the defect macro BOOST_NO_CXX14_HDR_SHARED_MUTEX. Currently gcc-5.1 and gcc-4.9+ support the shared_mutex header file in libstdc++. Also libc++ should support the shared_mutex header file, if it is available, through the SD-6 macros, which means that clang via its C++14 support should support it. Compiler support for C++14 which I have found is:  
  
clang 3.4, using -std=c++1y  
clang 3.5+, using -std=c++14  
gcc 4.8, using -std=c++1y  
gcc 4.9, using -std=c++1y  
gcc 5.1, using -std=c++14  
  
Currently no version of VC++ supports shared_mutex, but I anticipate the next version of VC++ may support it.  
  
The Sd-6 macros are used for standard libraries as a first check, followed by what we know of shared_mutex support from existing implementations. I am willing to update the config standard library headers, as necessary, as more standard libraries support shared_mutex outside of SD-6 macro use, but I anticipate that SD-6 will automatically do the job as it becomes adopted by compilers. The use of SD-6 to test for shared_mutex support in config incurs no overhead of included standard C++ library header files for end-users.

---

## Comment 1

> Username: morinmorin  
> Created_at: 2015-06-28 16:48:13 UTC  
> Updated_at: 2015-07-01 03:42:55 UTC  
> Url: https://github.com/boostorg/config/pull/66#discussion_r33424470  

Don't use version check for clang. It's vulnerable as different vendors of clang use their own version numbering system.   
  
I think you would need this  
  
```  
#if defined(__has_include)  
#  if !__has_include(<shared_mutex>)  
#    define BOOST_NO_CXX14_HDR_SHARED_MUTEX  
#  elif __cplusplus <= 201103L  
#    define BOOST_NO_CXX14_HDR_SHARED_MUTEX  
#  endif  
#else  
#  define BOOST_NO_CXX14_HDR_SHARED_MUTEX  
#endif  
```

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-06-28 17:09:55 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-116299465  

I corrected the PR as suggested.

---

## Comment 3

> Username: morinmorin  
> Created_at: 2015-06-29 01:35:41 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-116370059  

[From commit message]  
  
> clang doc says that SD-6 supported fron clang 3.4 and up.  
  
As a note, those newly introduced macros are `__cpp_constexpr` things (used as, e.g. `#if __cpp_constexpr < 201304`); As for `__has_include` macro, clang has already implemented it for more than five years.   
  
I don't know whether the macro proposed in this PR determines onlythe availability of the header or it also determines the "usability" (to some extent). If the latter is true, testing against `__has_include` is insufficient; most of the code in the header is turned off in C++03/11 mode. That's why I suggested this detection code:   
  
```  
#if defined(__has_include)  
#  if !__has_include(<shared_mutex>)  
#    define BOOST_NO_CXX14_HDR_SHARED_MUTEX  
#  elif __cplusplus <= 201103L  
#    define BOOST_NO_CXX14_HDR_SHARED_MUTEX  
#  endif  
#else  
#  define BOOST_NO_CXX14_HDR_SHARED_MUTEX  
#endif  
```

---

## Comment 4

> Username: eldiener  
> Created_at: 2015-06-29 03:53:54 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-116422218  

The code only determines the existence of the header. The actual test for the macro determines the usability of the header for a particular compiler. I am assuming that if the header exists, then it should be implemented correctly, and I don't think it is worthwhile to try to do otherwise as far as determining whether or not the macro should be defined or not.  
  
The actual code which attempts to determine whether the macro should be define or not does do a further check for standard libraries in which it is known that the __cplusplus can be relied on to tell me that C++14 is supported. But I am not just going to blindly assume this works for each standard library Boost supports.

---

## Comment 5

> Username: morinmorin  
> Created_at: 2015-06-29 07:16:03 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-116490412  

Sorry, "usability" was too ambiguous here:   
  
> > I don't know whether the macro proposed in this PR determines only the availability of the header or it also determines the "usability" (to some extent).  
  
By "usability", I meant the **existence** of `std::shared_mutex`.   
  
I recommended to do both __has_include check and C++ mode check, because libc++ in C++ 03/11 mode does not define any constructs in `<shared_mutex>`. (And, in libstdc++, including the header in C++03/11 mode makes compilation errors.) Note that the existing `BOOST_NO_CXX11_HDR_XXXXXX` macros do C++ mode checking, e.g.  
  
```  
#if (BOOST_LIBSTDCXX_VERSION < 50100) || !defined(BOOST_LIBSTDCXX11)  
```

---

## Comment 6

> Username: eldiener  
> Created_at: 2015-06-29 12:49:20 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-116646357  

The macro only is meant to test for the existence of the <shared_mutex> header. The C++ mode check is done for libstdc++ and libc++ in the current code.

---

## Comment 7

> Username: morinmorin  
> Created_at: 2015-06-29 14:21:20 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-116695343  

> The macro only is meant to test for the existence of the header.   
  
The macro should check C++ mode too.  
  
> The C++ mode check is done for libstdc++ and libc++ in the current code.  
  
Yes, the current code does the C++ mode check. But no, that's not what I have in mind; I mean this:   
  
```  
#if defined(__has_include)  
#  if !__has_include(<shared_mutex>)  
#    define BOOST_NO_CXX14_HDR_SHARED_MUTEX  
#  elif __cplusplus <= 201103L  
#    define BOOST_NO_CXX14_HDR_SHARED_MUTEX  
#  endif  
#else  
#  define BOOST_NO_CXX14_HDR_SHARED_MUTEX  
#endif  
```

---

## Comment 8

> Username: eldiener  
> Created_at: 2015-06-29 14:30:25 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-116704829  

I disagree with you. Please implement your own version if you feel otherwise.

---

## Comment 9

> Username: morinmorin  
> Created_at: 2015-06-30 00:47:44 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-116892118  

Like this (libc++: e5494f24f89fe1d675681c6b2ce38e2e2655161a, stdlibc++: 93bfd63fbf892a5752620c17f08474543cca542a)?

---

## Comment 10

> Username: eldiener  
> Created_at: 2015-06-30 01:54:30 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-116910665  

I don't understand your point. I have made my PR and I think it correct. John Maddock, who is the main developer of config, can decide if he wants to accept my PR or not.  
  
If you think another PR doing the same thing is better why not just make your own PR and see if that is accepted instead. I have already said that I don't think that it is necessary to do what you are doing. Your code is essentially saying "Despite SD-6 telling me whether the header file exists or not I am not going to believe the SD-6 implementation is correct if it tells me that the header exists, but I am going to decide on whether the header exists or not based on the value of value of __cplusplus." I do not understand that logic and why you think the value of __cplusplus should override what the SD-6 implementation tells you.  
  
In my own implementation I only look at the value of __cplusplus only if SD-6 is not being implemented. I do that in my code, for certain standard libraries, because it had been previously done and I trust prior art. If SD-6 is being implemented I trust that it is implemented correctly. If you code as if SD-6 is not being implemented correctly then your point of view, it seems to me, is that we should just throw it away and disregard it. Needless to say I do not share that opinion.  
  
If actual tests show problems in my code, if it gets merged to 'develop', I am perfectly willing to change it per one-off situations based on a particular compiler's implementation.

---

## Comment 11

> Username: morinmorin  
> Created_at: 2015-06-30 04:00:30 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-116940648  

Edward,  
  
I understand your point that the macro should check only the the existence of the header.   
I just tried to follow the existing detection style (e.g.  
  
```  
#if (BOOST_LIBSTDCXX_VERSION < 50100) || !defined(BOOST_LIBSTDCXX11)  
```  
  
, which I understand as the negation of "stdlib has an appropriate header AND we're in suitable C++ mode"). But I admit that I was too interrupting.   
  
I'm sorry for my bad manners. And thanks for your time.   
  
Regards,  
Michel

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2015-06-30 08:14:36 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-117051213  

Edward, there still seem to be a number of issues with this patch:  
- There's no need for any checks in the compiler/*.hpp files, this should all be handled by the std lib config files (those are GCC-alike compilers that use libstdc++ in any case).  
- In general Michel is correct that we need a check on C++ version as well, for example GCC (only thing I have to check this with right now) reports __has_include(<shared_mutex>) as true in C++03 mode, but is obviously unable actually to Grok the header.  I'm sure the same is true of other compilers - __has_Include is essentially a preprocessor mechanism that known nothing about whether the header is actually usable.  
- I've checked and this fails with GCC in C++03 or 11 mode:  
  
#include <boost/config.hpp>  
  
#if !defined(BOOST_NO_CXX14_HDR_SHARED_MUTEX)  
#include <shared_mutex>  
#endif  
  
However, the tests all pass, so there's something wrong with the tests somewhere I suspect?  Apologies I haven't had time to dig into the details.

---

## Comment 13

> Username: eldiener  
> Created_at: 2015-06-30 13:14:40 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-117176699  

I checked the compiler/*.hpp files for PGI and Pathscale because each of them already had defines for  BOOST_NO_CXX11_HDR_ situations. I will remove the check there for shared_mutex and rely on the libstdc++ check.  
  
Apologies to Michel but I did not understand why he was checking __cplusplus. Now that you have explained it I can see that he was right and I was wrong. I will correct my check accordingly.  
  
Your example about failure with GCC in C++03 or 11 mode did not come out properly. I think in this markdown you need to indent code to have everything show properly. I will check on my own that gcc works properly in C++03, C++11, and C++14 modes after I update the code.

---

## Comment 14

> Username: eldiener  
> Created_at: 2015-07-01 04:05:43 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-117428315  

I have refined the logic for determining when BOOST_NO_CXX14_HDR_SHARED_MUTEX should be defined. For libstdc++ and libc++ I have done it based on what I have tested for gcc-4.8, gcc-4.9, and gcc-5.1, as well as clang 3.4, clang 3.5, clang 3.6, and clang 3.7. I have not found any other compilers currently with potential C++14 support so my logic there is based strictly on the C++14 mandated value for __cplusplus ( 201402 ).  
  
For the clang and gcc versions which have potential C++14 support all of the current values for C++03 and C++11 for __cplusplus are respectively 199711 and 201103. For C++14 support for __cplusplus the gcc-5.1, clang 3.5, clang 3.6, and the current development clang-3.7 are all 201402. For gcc-4.9 ( c++1y ) the value is 201300, for gcc-4.8 ( c++1y ) the value is still 201103, and for clang-3.4 ( c++1y ) the value is 201305. The evidence is that gcc-4.9 does support shared mutex, gcc-4.8 does not support shared_mutex, and I am not sure if clang-3.4 supports shared mutex but its value says that it does in my code as long as the header exists.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2015-07-07 10:19:54 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-119160652  

Apologies for the delay in getting to this, confirmed it seems to be working OK, merged.

---

## Comment 16

> Username: eldiener  
> Created_at: 2015-07-07 13:57:38 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-119209745  

Thanks !

---

## Comment 17

> Username: morinmorin  
> Created_at: 2015-07-07 16:49:11 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-119265507  

Thanks for your work, Edward!  
Also thanks to John!

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2015-07-25 12:28:23 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-124841413  

The test case was failing for clang/libc++ - <shared_mutex> is present, but does _not_ set the SD6 macros, so the test case fell through to testing for std::shared_mutex - but that's a C++17 feature not a C++14 one.  Also VC14 supports this.  
  
See https://github.com/boostorg/config/commit/dfef18919767f7ccc438d3133e7503751aa71018  
and https://github.com/boostorg/config/commit/d5839ad09b1bebe03f5ed0c9eaecff80b35e7409

---

## Comment 19

> Username: eldiener  
> Created_at: 2015-07-25 13:12:52 UTC  
> Url: https://github.com/boostorg/config/pull/66#issuecomment-124846537  

I understand your changes, especially as VC++14 can now be empirically determined.  
  
The std::shared_timed_mutex was originally std::shared_mutex in the C++14 specification, which is why I coded the original test case as I did. I realize that afterward for C++14 std::shared_mutex became std::shared_timed_mutex and std::shared_mutex was designated as a non-timed implementation for C++17.  
  
I understand that if a compiler has an implementation of std::shared_timed_mutex but does not set the SD6 macros the test will erroneously fail. It is unfortunate that clang and VC++14 do not support the SD6 macros while still supporting std::shared_timed_mutex.

---
