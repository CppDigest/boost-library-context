# #42 Check for _POSIX_TIMEOUTS more neatly. [Closed]

> Username: ya1gaurav  
> Created at: 2015-01-29 09:44:55 UTC  
> Updated at: 2017-10-11 23:24:56 UTC  
> Closed at: 2017-10-11 23:24:56 UTC  
> Url: https://github.com/boostorg/thread/pull/42  

The present code do twice checking using "&&" operator.  
Making the check more neat.   
Refer : https://sourceware.org/git/?p=glibc.git;a=blob;f=include/features.h   
258 #if (_POSIX_C_SOURCE - 0) >= 200112L  
Also Refer : http://man7.org/linux/man-pages/man7/feature_test_macros.7.html for explanation of feature test macros.

---

## Comment 1

> Username: ya1gaurav  
> Created_at: 2015-02-04 05:51:29 UTC  
> Url: https://github.com/boostorg/thread/pull/42#issuecomment-72794721  

It is enough,  but as I mentioned a link, it seems more generic way for such checking.  
_POSIX_C_SOURCE/_POSIX_TIMEOUTS    
If ==1, like _POSIX_SOURCE; if >=2 add IEEE Std 1003.2;  
if >=199309L, add IEEE Std 1003.1b-1993;  
if >=199506L, add IEEE Std 1003.1c-1995;  
if >=200112L, all of IEEE 1003.1-2004  
if >=200809L, all of IEEE 1003.1-2008

---

## Comment 2

> Username: austin-beer  
> Created_at: 2017-10-11 22:39:46 UTC  
> Url: https://github.com/boostorg/thread/pull/42#issuecomment-335969245  

Just an FYI, these changes were independently committed in https://github.com/boostorg/thread/commit/c7bb6fa318ddc27cb1ad9497f295db2744c0f069, so this PR can be closed.

---

## Comment 3

> Username: viboes  
> Created_at: 2017-10-11 23:24:50 UTC  
> Url: https://github.com/boostorg/thread/pull/42#issuecomment-335976507  

Where I have my head!

---
