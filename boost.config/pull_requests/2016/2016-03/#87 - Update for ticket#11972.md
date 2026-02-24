# #87 Update for ticket#11972 [Closed]

> Username: akumta  
> Created at: 2016-03-03 21:48:23 UTC  
> Updated at: 2016-09-06 17:01:35 UTC  
> Closed at: 2016-09-06 17:01:35 UTC  
> Url: https://github.com/boostorg/config/pull/87  



---

## Comment 1

> Username: eldiener  
> Created_at: 2016-03-03 23:48:57 UTC  
> Url: https://github.com/boostorg/config/pull/87#issuecomment-192024674  

What makes you think that all compilers are distributed with unistd.h and why would you want to include it before the include guard and not when it might be needed ?

---

## Comment 2

> Username: akumta  
> Created_at: 2016-03-04 01:11:57 UTC  
> Url: https://github.com/boostorg/config/pull/87#issuecomment-192045278  

My bad.  However, I'm not sure which compilers would be distributed with   
unistd.h.  
OK, I see  BOOST_HAS_UNISTD_H macro.  
Would this be OK?  
  
#if defined BOOST_HAS_UNISTD_H  
#include <unistd.h>  
#endif  
  
Thanks.  
  
On 03/03/16 15:48, Edward Diener wrote:  
  
> What makes you think that all compilers are distributed with unistd.h   
> and why would you want to include it before the include guard and not   
> when it might be needed ?  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/config/pull/87#issuecomment-192024674.

---

## Comment 3

> Username: akumta  
> Created_at: 2016-03-06 12:55:36 UTC  
> Url: https://github.com/boostorg/config/pull/87#issuecomment-192885003  

Does the solution above seem OK?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2016-03-06 18:25:39 UTC  
> Url: https://github.com/boostorg/config/pull/87#issuecomment-192954641  

This doesn't look right to me, currently we have this logic already in posix_features.hpp - but only to set BOOST_HAS_POSIX_THREADS, this may then get #undef'ed later if the compiler isn't in thread safe mode.  In general unistd.h only tells you about the operating system, it tells you nothing about whether the compiler can generate thread safe code in it's current mode.  If the latest Oracle compiler really does always generate thread safe code irrespective of what compiler flags are passed then:  
- The change should be made to compiler/sun.hpp, and  
- The change should be guarded by a compiler version check.  
  
In any case, the documentation for 12.5 still lists -mt as an option: https://docs.oracle.com/cd/E60778_01/html/E60746/bkacw.html#OSSCPbkaeb and the man page (https://docs.oracle.com/cd/E60778_01/html/E61993/uc-cc-1.html#scrolltoc) says:_  
  
"-mt[={yes|no}]  
  
```  
Use this option to compile and link multithreaded code using the Oracle Solaris threads or POSIX threads API. The -mt=yes option assures that libraries are linked in the appropriate order.  
  
This option passes -D_REENTRANT to the preprocessor."_  
```  
  
Which suggests something else is going on the the original bug report?

---

## Comment 5

> Username: akumta  
> Created_at: 2016-08-31 20:19:41 UTC  
> Url: https://github.com/boostorg/config/pull/87#issuecomment-243887953  

The elimination of _REENTRANT as mentioned in the bug report is on Solaris 12, which is internally available.  
The following change seems to work.  
%diff ./suffix.hpp suffix.hpp_orig  
249c249  
  
## < #if (defined(**MT**) || defined(_MT) || defined(_REENTRANT)  || defined(__SunOS_5_12) \  
  
> #if (defined(**MT**) || defined(_MT) || defined(_REENTRANT) \  
> %   
> If acceptable, I can modify this PR and submit.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2016-09-02 17:24:26 UTC  
> Url: https://github.com/boostorg/config/pull/87#issuecomment-244436992  

I'll repeat what I said earlier: suffix.hpp is the wrong place to fix this.  sun.hpp should set BOOST_HAS_THREADS dependent upon the compiler version which introduces this change.

---

## Comment 7

> Username: akumta  
> Created_at: 2016-09-02 17:37:34 UTC  
> Url: https://github.com/boostorg/config/pull/87#issuecomment-244440164  

Got it. Will close this PR and submit a change to sunpro_cc.hpp, shortly.

---
