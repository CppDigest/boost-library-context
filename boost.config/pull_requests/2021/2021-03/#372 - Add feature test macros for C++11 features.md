# #372 Add feature test macros for C++11 features. [Merged]

> Username: Breakthrough  
> Created at: 2021-03-12 03:07:39 UTC  
> Updated at: 2021-03-13 06:31:00 UTC  
> Merged at: 2021-03-12 17:29:11 UTC  
> Closed at: 2021-03-12 17:29:11 UTC  
> Url: https://github.com/boostorg/config/pull/372  

This PR adds some feature test macros for the C++11 features.  I wasn't sure about decltype, so left that as-is for now, but documented what the appropriate check should be.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-03-12 17:29:06 UTC  
> Url: https://github.com/boostorg/config/pull/372#issuecomment-797640978  

That looks fair enough.... merging.  
  
Yes your comment about __decltype looks correct to me, __cpp_rvalue_references for BOOST_NO_CXX11_RVALUE_REFERENCES, there may be others.  
  
Are you testing on a specific compiler?  I didn't think many were using the EDG frontend these days.

---

## Comment 2

> Username: Breakthrough  
> Created_at: 2021-03-13 06:31:00 UTC  
> Url: https://github.com/boostorg/config/pull/372#issuecomment-797877037  

Specifically GreenHills in this case.  I initially considered just modifying that compiler header, but then I noticed that the C++14 feature test macros were already accounted for in the EDG-common checks, so figured might as well update all the C++11 ones to match.  This allows a few more boost libraries to compile out of the box now.  
  
Thanks so much for the quick response, as well as for your work on boost!!

---
