# #29 Fix multi-line comment [Merged]

> Username: CromwellEnage  
> Created at: 2018-11-11 14:28:20 UTC  
> Updated at: 2018-11-12 13:16:28 UTC  
> Merged at: 2018-11-12 12:46:25 UTC  
> Closed at: 2018-11-12 12:46:25 UTC  
> Url: https://github.com/boostorg/parameter/pull/29  



---

## Comment 1

> Username: CromwellEnage  
> Created_at: 2018-11-11 17:33:34 UTC  
> Url: https://github.com/boostorg/parameter/pull/29#issuecomment-437688771  

To elaborate, some tests in other libraries (e.g. Boost.Graph) are emitting the following warning.  
  
../../../boost/parameter/aux_/tagged_argument.hpp:643:1: warning: multi-line comment [-Wcomment]  
  
This PR aims to eliminate the warning.

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-11-12 02:19:23 UTC  
> Url: https://github.com/boostorg/parameter/pull/29#issuecomment-437731908  

I do not understand your fix.

---

## Comment 3

> Username: CromwellEnage  
> Created_at: 2018-11-12 02:54:27 UTC  
> Url: https://github.com/boostorg/parameter/pull/29#issuecomment-437736673  

The original fix was meant to eliminate warnings about multi-line comments that were showing up in tests for other libraries, such as Boost.Graph.  In retrospect, however, I should have kept it simple in the first place with this fix I just submitted.

---

## Comment 4

> Username: eldiener  
> Created_at: 2018-11-12 03:13:21 UTC  
> Url: https://github.com/boostorg/parameter/pull/29#issuecomment-437739373  

Your original fix was wrong AFAIK. Placing a continuation character after a single line comment does nothing.  
  
The updated fix works although the original warning is perplexing. Why have a compiler warning when you have two consecutive single line comments ? BTW if you wanted to keep the comment but avoid the warning you could have changed it to:  
  
```  
/* #if !defined(BOOST_NO_FUNCTION_TEMPLATE_ORDERING) && \  
      !BOOST_WORKAROUND(__BORLANDC__, BOOST_TESTED_AT(0x564)) */  
```

---

## Comment 5

> Username: CromwellEnage  
> Created_at: 2018-11-12 03:37:55 UTC  
> Url: https://github.com/boostorg/parameter/pull/29#issuecomment-437743031  

I'll keep the C-style comment suggestion in mind for next time.  Thanks!

---
