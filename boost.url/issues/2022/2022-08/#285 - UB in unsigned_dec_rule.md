# #285 - UB in unsigned_dec_rule [Closed]

> Username: alandefreitas  
> Created at: 2022-08-01 15:49:23 UTC  
> Updated at: 2022-08-03 22:15:24 UTC  
> Closed at: 2022-08-03 22:15:24 UTC  
> Url: https://github.com/boostorg/url/issues/285  

https://github.com/CPPAlliance/url/blob/develop/include/boost/url/grammar/impl/unsigned_dec_rule.hpp#L47  
  
`it + Digits10` is not allowed to be calculated if the result would be outside the buffer, even if you are taking the `min` of it with `end`
