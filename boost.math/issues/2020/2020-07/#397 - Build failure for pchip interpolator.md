# #397 - Build failure for pchip interpolator [Closed]

> Username: jothalha  
> Created at: 2020-07-08 07:38:18 UTC  
> Updated at: 2020-07-08 12:27:39 UTC  
> Closed at: 2020-07-08 12:27:39 UTC  
> Url: https://github.com/boostorg/math/issues/397  

In the boost::math::interpolators::pchip ctor the *using std::isnan* declaration is missing. On the other hand, in pchip::push_back both *using std::abs* and *using std::isnan* are declared but unused.  
  
```  
include/boost/math/interpolators/pchip.hpp:28:18: error: ‘isnan’ was not declared in this scope; did you mean ‘std::isnan’?  
   28 |         if (isnan(left_endpoint_derivative))  
      |             ~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~  
      |             std::isnan  
include/boost/math/interpolators/pchip.hpp:59:18: error: ‘isnan’ was not declared in this scope; did you mean ‘std::isnan’?  
   59 |         if (isnan(right_endpoint_derivative))  
      |             ~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~  
      |             std::isnan  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-07-08 12:25:47 UTC  
> Url: https://github.com/boostorg/math/issues/397#issuecomment-655487220  

Fixed in 6bb0e8dee02e; thanks!
