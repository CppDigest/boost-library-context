# #171 Add code to set for BOOST_NO_CXX98_RANDOM_SHUFFLE and BOOST_NO_CXX98_… [Merged]

> Username: mclow  
> Created at: 2017-08-07 00:54:54 UTC  
> Updated at: 2017-08-09 16:57:02 UTC  
> Merged at: 2017-08-07 15:23:50 UTC  
> Closed at: 2017-08-07 15:23:50 UTC  
> Url: https://github.com/boostorg/config/pull/171  

…BINDERS correctly  
  
This will fix compilation failures when building Boost.Test with clang v.50 and -std=c++1z or 2a

---

## Review 1 [Commented]

> Username: eldiener  
> Created_at: 2017-08-07 04:30:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/171#pullrequestreview-54563261  

_LIBCPP_ENABLE_CXX17_REMOVED_RANDOM_SHUFFLE is used for the binders. Can that be correct ?

---

## Comment 2

> Username: mclow  
> Created_at: 2017-08-07 13:12:10 UTC  
> Url: https://github.com/boostorg/config/pull/171#issuecomment-320659275  

Sorry for the copy/paste error. Updated.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-08-07 18:09:08 UTC  
> Url: https://github.com/boostorg/config/pull/171#issuecomment-320737928  

Thanks chaps.

---

## Comment 4

> Username: mclow  
> Created_at: 2017-08-09 15:48:03 UTC  
> Url: https://github.com/boostorg/config/pull/171#issuecomment-321296713  

John - can you merge this change to master, please? I'd like to get this in the 1.65.0 release.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2017-08-09 16:57:02 UTC  
> Url: https://github.com/boostorg/config/pull/171#issuecomment-321316237  

On 09/08/2017 16:48, Marshall Clow wrote:  
>  
> John - can you merge this change to master, please? I'd like to get   
> this in the 1.65.0 release.  
>  
  
Done.  
  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---
