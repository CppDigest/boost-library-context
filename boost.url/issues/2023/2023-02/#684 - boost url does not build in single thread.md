# #684 - boost url does not build in single thread [Closed]

> Username: ropieur  
> Created at: 2023-02-15 11:54:09 UTC  
> Updated at: 2023-03-13 08:10:24 UTC  
> Closed at: 2023-02-22 01:11:06 UTC  
> Url: https://github.com/boostorg/url/issues/684  

Hi,  
I am wondering the reason why boost url couldn't be used in single threaded.  
Woudn't be possible to allow building it in single thread?  
Thank you

---

## Comment 1

> Username: pdimov  
> Created at: 2023-02-15 15:54:59 UTC  
> Url: https://github.com/boostorg/url/issues/684#issuecomment-1431591861  

What specific issue are you having?

---

## Comment 2

> Username: ropieur  
> Created at: 2023-02-15 16:05:59 UTC  
> Url: https://github.com/boostorg/url/issues/684#issuecomment-1431609526  

Our internal crosscompiler mingw32 (single thread compiler) complaints about std::mutex.  
  
```  
10:20:19  In file included from ./boost/url/grammar/impl/range_rule.hpp:16,  
10:20:19                   from ./boost/url/grammar/range_rule.hpp:600,  
10:20:19                   from ./boost/url/grammar.hpp:29,  
10:20:19                   from ./boost/url.hpp:13,  
10:20:19                   from ./boost/url/src.hpp:28,  
10:20:19                   from libs/url/src/src.cpp:10:  
10:20:19  ./boost/url/grammar/recycled.hpp:108:10: error: 'mutex' in namespace 'std' does not name a type  
10:20:19    108 |     std::mutex m_;  
10:20:19        |          ^~~~~  
10:20:19  ./boost/url/grammar/recycled.hpp:19:1: note: 'std::mutex' is defined in header '<mutex>'; did you forget to '#include <mutex>'?  
10:20:19     18 | #include <stddef.h> // ::max_align_t  
10:20:19    +++ |+#include <mutex>  
10:20:19     19 |   
  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-02-17 15:07:12 UTC  
> Updated at: 2023-02-17 15:39:33 UTC  
> Url: https://github.com/boostorg/url/issues/684#issuecomment-1434777364  

Alan, some notes for fixing this:  
  
> mutex doesn't work in a single threaded build of libstdc++. you need to, say, provide a macro disabling the mutex in single threaded mode like BOOST_SYSTEM_DISABLE_THREADS:  
  
  
https://github.com/boostorg/system/commit/53c00841fc0d892bf43cda60e3ea2f05c4362b32

---

## Comment 4

> Username: ropieur  
> Created at: 2023-02-17 15:09:42 UTC  
> Url: https://github.com/boostorg/url/issues/684#issuecomment-1434781194  

Thank you @vinniefalco . May I expect to see this improvement in the next release of boost (1.82.0) ?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-02-17 15:25:29 UTC  
> Url: https://github.com/boostorg/url/issues/684#issuecomment-1434802459  

Yes

---

## Comment 6

> Username: ropieur  
> Created at: 2023-02-17 15:30:59 UTC  
> Url: https://github.com/boostorg/url/issues/684#issuecomment-1434809689  

Great to hear. I close the ticket.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2023-02-17 15:37:56 UTC  
> Url: https://github.com/boostorg/url/issues/684#issuecomment-1434818355  

Well lets keep it open so Alan knows to work on it :)

---

## Comment 8

> Username: alandefreitas  
> Created at: 2023-02-21 18:04:04 UTC  
> Url: https://github.com/boostorg/url/issues/684#issuecomment-1438895964  

@ropieur could you try [this branch](https://github.com/boostorg/url/pull/689)? This implementation uses [BOOST_SYSTEM_DISABLE_THREADS](https://github.com/boostorg/system/commit/53c00841fc0d892bf43cda60e3ea2f05c4362b32) as a reference.  
  
(I'm duplicating https://github.com/boostorg/url/pull/689#issuecomment-1437672781 in case @ropieur is getting notifications from the issue only)

---

## Comment 9

> Username: ropieur  
> Created at: 2023-03-13 08:10:24 UTC  
> Url: https://github.com/boostorg/url/issues/684#issuecomment-1465684359  

@alandefreitas,  
Thank you for the fix. Sorry for the delay. I will check internally how we can verify the branch.
