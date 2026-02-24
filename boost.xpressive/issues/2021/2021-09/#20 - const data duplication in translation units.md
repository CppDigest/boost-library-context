# #20 - const data duplication in translation units [Open]

> Username: matbech  
> Created at: 2021-09-04 18:10:53 UTC  
> Updated at: 2021-09-04 18:37:22 UTC  
> Url: https://github.com/boostorg/xpressive/issues/20  

[SizeBench](https://www.microsoft.com/en-us/p/sizebench/9ndf4n1wg7d6) has identified that the following static variables are duplicated in every translation unit:  
https://github.com/boostorg/xpressive/blob/master/include/boost/xpressive/detail/core/icase.hpp#L26  
https://github.com/boostorg/xpressive/blob/master/include/boost/xpressive/detail/utility/sequence_stack.hpp#L25  
https://github.com/boostorg/xpressive/blob/master/include/boost/xpressive/regex_primitives.hpp#L635  
https://github.com/boostorg/xpressive/blob/master/include/boost/xpressive/regex_primitives.hpp#L243  
https://github.com/boostorg/xpressive/blob/master/include/boost/xpressive/regex_primitives.hpp#L496  
https://github.com/boostorg/xpressive/blob/master/include/boost/xpressive/regex_primitives.hpp#L404  
etc  
  
The proposed change using BOOST_INLINE_CONSTEXPR:  
```  
BOOST_INLINE_CONSTEXPR detail::modifier_op<detail::icase_modifier> icase = {{}, regex_constants::icase_};  
```  
  
PR https://github.com/boostorg/xpressive/pull/21
