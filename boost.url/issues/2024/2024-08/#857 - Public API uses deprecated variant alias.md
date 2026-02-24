# #857 - Public API uses deprecated variant alias [Closed]

> Username: alandefreitas  
> Created at: 2024-08-15 14:57:08 UTC  
> Updated at: 2024-08-20 00:48:55 UTC  
> Closed at: 2024-08-20 00:48:55 UTC  
> Url: https://github.com/boostorg/url/issues/857  

Boost.Url is using an alias to boost::variant2 it deprecated, which causes a warning number bump.  
  
https://github.com/boostorg/url/blob/develop/include/boost/url/grammar/variant_rule.hpp#L82  
  
Although this is not failing, it emits a deprecation warning for a public API.
