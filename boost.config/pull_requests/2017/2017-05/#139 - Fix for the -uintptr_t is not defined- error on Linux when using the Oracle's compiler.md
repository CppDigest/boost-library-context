# #139 Fix for the "uintptr_t is not defined" error on Linux when using the Oracle's compiler [Closed]

> Username: mkartashev  
> Created at: 2017-05-23 10:51:07 UTC  
> Updated at: 2017-06-21 10:58:54 UTC  
> Closed at: 2017-06-21 10:58:54 UTC  
> Url: https://github.com/boostorg/config/pull/139  

Lots of "uintptr_t is not defined" errors when building on Oracle Linux with  
the Oracle compiler; the fix is to define BOOST_HAS_STDINT_H in the compiler's  
configuration header sunpro_cc.hpp.  
  
The corresponding ticket is https://svn.boost.org/trac/boost/ticket/13045

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-06-12 18:45:39 UTC  
> Url: https://github.com/boostorg/config/pull/139#issuecomment-307882738  

I think some more investigation is required here... I'd rather not define BOOST_HAS_STDINT_H unconditionally, as while that may be true for the latest compiler releases, it's not for older ones in general.  This also works fine for me with Oracle C++ on Ubuntu linux (though I realise that's not an officially supported option).  
  
There are 2 places where this should be picked up - in posix_features.hpp via:  
  
`  
      // POSIX version 6 requires <stdint.h>  
#     if defined(_POSIX_VERSION) && (_POSIX_VERSION >= 200100)  
#        define BOOST_HAS_STDINT_H  
#     endif  
`  
  
Which is what gets triggered for me on Ubuntu.  There is also detection logic in linux.hpp, but this excludes compilers which don't define __GNUC__:  
  
`  
#if defined(__GLIBC__) && ((__GLIBC__ > 2) || ((__GLIBC__ == 2) && (__GLIBC_MINOR__ >= 1)))  
   // <stdint.h> defines int64_t unconditionally, but <sys/types.h> defines  
   // int64_t only if __GNUC__.  Thus, assume a fully usable <stdint.h>  
   // only when using GCC.  
#  if defined(__GNUC__)  
#    define BOOST_HAS_STDINT_H  
#  endif  
#endif  
`  
  
As far as I can tell, this check for __GNUC__ is now outdated on all recent glibc releases, so can you please test the patch in https://github.com/boostorg/config/pull/155 and verify that this works for you?    
  
Thanks.

---

## Comment 2

> Username: mkartashev  
> Created_at: 2017-06-21 10:58:54 UTC  
> Url: https://github.com/boostorg/config/pull/139#issuecomment-310043586  

(sorry for the late reply, I was away for a while)  
  
Yes, I see your point. Extending the OS-specific config looks reasonable and the Solaris header already has  BOOST_HAS_STDINT_H defined.   
  
I tested the patch in #155 and can confirm that it resolves the bug that I filed (#13045). Hope it gets integrated soon!

---
