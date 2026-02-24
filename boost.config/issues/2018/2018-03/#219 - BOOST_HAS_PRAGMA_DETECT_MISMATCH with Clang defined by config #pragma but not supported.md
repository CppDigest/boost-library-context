# #219 - BOOST_HAS_PRAGMA_DETECT_MISMATCH with Clang defined by config #pragma but not supported. [Closed]

> Username: pabristow  
> Created at: 2018-03-21 17:50:04 UTC  
> Updated at: 2020-03-10 12:27:56 UTC  
> Closed at: 2020-03-10 12:27:56 UTC  
> Url: https://github.com/boostorg/config/issues/219  

I am compiling Boost.Math tests (using the jamfile) with Clang 6.0.0 on windows 10 and get this warning  
  
In file included from ..\..\..\boost/function/function_base.hpp:21:  
..\..\..\boost/type_index.hpp:37:20: warning: unknown pragma ignored [-Wunknown-pragmas]  
#           pragma detect_mismatch( "boost__type_index__abi", "RTTI is used")  
                   ^  
Clang (and GCC?) do not seem to provide this pragma.  
  
In \modular-boost\libs\type_index\include\boost\type_index.hpp  
  
at line 43  
  
#   ifdef BOOST_HAS_PRAGMA_DETECT_MISMATCH  
#       pragma detect_mismatch( "boost__type_index__abi", "RTTI is off - using CTTI")  
#   end  
  
(and attempts to suppress with -Wno-unknown-pragmas - elswhere reported to have no effect).  
  
while I:\modular-boost\libs\type_index\include\boost\type_index.hpp  says  
  
// Detecting `-fms-extension` compiler flag assuming that _MSC_VER defined when that flag is used.  
#if defined (_MSC_VER) && (__clang_major__ > 3 || (__clang_major__ == 3 && __clang_minor__ >= 4))  
#   define BOOST_HAS_PRAGMA_DETECT_MISMATCH  
#endif  
  
does not seem to give the expected non-define of BOOST_HAS_PRAGMA_DETECT_MISMATCH  
  
BOOST_HAS_PRAGMA_DETECT_MISMATCH is defined, but using Clang 6.0.0 does not support this pragma.  
  
However perhaps this is intentional and it is better to have this warning (whose significance, if any, I have yet to understand)?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-04-18 19:10:05 UTC  
> Url: https://github.com/boostorg/config/issues/219#issuecomment-382496895  

I'm unable to reproduce with clang-6 installed in the Visual studio IDE and -W4.  I've just added a test case for this: https://github.com/boostorg/config/commit/39f48141c5d50f6203590e6153a76c042e4c465e

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-08-03 17:13:00 UTC  
> Url: https://github.com/boostorg/config/issues/219#issuecomment-410318465  

Ping?  Do we know what's needed to reproduce?

---

## Comment 3

> Username: pabristow  
> Created at: 2020-03-10 12:27:56 UTC  
> Url: https://github.com/boostorg/config/issues/219#issuecomment-597059431  

I no longer have Clang 6 available so cannot reproduce or retest.  But the added test case should reveal if anything is still wrong.
