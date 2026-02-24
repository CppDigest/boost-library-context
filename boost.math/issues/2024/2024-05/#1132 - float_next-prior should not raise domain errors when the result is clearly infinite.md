# #1132 - float_next/prior should not raise domain errors when the result is clearly infinite [Closed]

> Username: jzmaddock  
> Created at: 2024-05-08 17:03:41 UTC  
> Updated at: 2024-05-18 08:21:43 UTC  
> Closed at: 2024-05-18 08:21:43 UTC  
> Url: https://github.com/boostorg/math/issues/1132  

For example float_next(+INF) should be +INF and not a domain_error.  
  
See https://github.com/boostorg/multiprecision/pull/618.
