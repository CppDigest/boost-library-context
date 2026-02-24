# #32 - Use implementation from Describe's pp_for_each.hpp for `BOOST_PARSER_DEFINE_RULES` [Closed]

> Username: tzlaine  
> Created at: 2023-12-29 20:54:21 UTC  
> Updated at: 2023-12-31 03:06:04 UTC  
> Closed at: 2023-12-31 03:06:04 UTC  
> Url: https://github.com/boostorg/parser/issues/32  

Peter Dimov via Boost  
  
Zach Laine wrote:  
> 1) To detect Boost.Preprocessor headers.  If detected, Boost.Parser defines a  
> convenience macro that uses Boost.Preprocessor macros.  
  
Feel free to steal  
  
https://github.com/boostorg/describe/blob/develop/include/boost/describe/detail/pp_for_each.hpp
