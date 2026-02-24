# #48 - GCC suggest-override warning [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-13 01:51:11 UTC  
> Updated at: 2020-05-20 14:11:47 UTC  
> Closed at: 2020-05-20 14:11:47 UTC  
> Url: https://github.com/boostorg/property_tree/issues/48  

Warning when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/property_tree/detail/rapidxml.hpp:61:29: warning: ‘virtual const char* boost::property_tree::detail::rapidxml::parse_error::what() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-05-19 22:48:06 UTC  
> Url: https://github.com/boostorg/property_tree/issues/48#issuecomment-631127490  

See fixes in #53.

---

## Comment 2

> Username: EugeneZelenko  
> Created at: 2020-05-20 14:11:35 UTC  
> Url: https://github.com/boostorg/property_tree/issues/48#issuecomment-631498594  

Fixed in #53.
