# #88 Fix for Clang 3.4 (regression due to unguarded use of __is_identifier macro) [Closed]

> Username: morinmorin  
> Created at: 2016-03-30 10:01:56 UTC  
> Updated at: 2016-03-31 10:16:00 UTC  
> Closed at: 2016-03-31 08:10:43 UTC  
> Url: https://github.com/boostorg/config/pull/88  

`__is_identifier` macro is introduced in Clang 3.5. It is not supported in Clang 3.4 (released in 2014) or previous versions. Thus, f84f27c366cc5028587efc1e34808f141ab77861 breaks Clang 3.4. We need to guard the macro by either way of:  
  
(A)  
  
```  
#ifndef __is_identifier  
#define __is_identifier(x) 1  
#endif  
```  
  
Note the default value `1`. Since `__is_identifier(x)` is used to check the availability of some feature by asking "is `x` not a keyword-like word?", it makes sense to set the default value to `1`. libc++ sets the default value to 1, too.  
  
(B)  
  
```  
#ifdef __is_identifier  
#  if !__is_identifier(__int64) && !defined(__GNUC__)  
#    define BOOST_HAS_MS_INT64  
#  endif  
#endif  
```  
  
This PR implements (A).

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2016-03-31 08:10:43 UTC  
> Url: https://github.com/boostorg/config/pull/88#issuecomment-203809002  

This should now be fixed in develop.

---

## Comment 2

> Username: morinmorin  
> Created_at: 2016-03-31 10:15:59 UTC  
> Url: https://github.com/boostorg/config/pull/88#issuecomment-203866150  

Compiles fine now, thanks!

---
