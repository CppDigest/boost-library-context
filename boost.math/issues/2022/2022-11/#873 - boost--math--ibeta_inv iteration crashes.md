# #873 - boost::math::ibeta_inv iteration crashes [Closed]

> Username: linolinco  
> Created at: 2022-11-11 12:14:32 UTC  
> Updated at: 2022-11-15 09:00:40 UTC  
> Closed at: 2022-11-15 09:00:40 UTC  
> Url: https://github.com/boostorg/math/issues/873  

Calling `boost::math::ibeta_inv(1e50, 10, 0.1)` produces:  
`libc++abi.dylib: terminating with uncaught exception of type boost::wrapexcept<std::domain_error>: Error in function boost::math::ibeta<long double>(long double, long double, long double): Parameter x outside the range [0,1] in the incomplete beta function (got x=835.800004005432128906).`  
  
I suspect the inverse iteration for ibeta is thrown off by the large alpha.
