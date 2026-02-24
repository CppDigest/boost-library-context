# #109 Put explicit deprecation warning on boost/math/common_facto… [Merged]

> Username: NAThompson  
> Created at: 2018-02-10 19:25:16 UTC  
> Updated at: 2018-02-16 18:51:39 UTC  
> Merged at: 2018-02-15 11:55:28 UTC  
> Closed at: 2018-02-15 11:55:28 UTC  
> Url: https://github.com/boostorg/math/pull/109  

…r.hpp

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-02-11 11:37:09 UTC  
> Url: https://github.com/boostorg/math/pull/109#issuecomment-364744887  

Good idea, however we've just added  BOOST_HEADER_DEPRECATED(A) for this which will give better error messages - can you change it over?  Usage is   
  
```  
BOOST_HEADER_DEPRECATED("<boost/math/common_factor.hpp>")  
```  
  
Thanks!

---
