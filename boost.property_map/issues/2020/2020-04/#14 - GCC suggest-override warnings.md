# #14 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-13 01:50:07 UTC  
> Updated at: 2022-04-23 16:50:02 UTC  
> Closed at: 2022-04-23 16:50:02 UTC  
> Url: https://github.com/boostorg/property_map/issues/14  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/property_map/dynamic_property_map.hpp:112:15: warning: ‘virtual const char* boost::dynamic_const_put_error::what() const’ can be marked override [-Wsuggest-override]  
./boost/property_map/dynamic_property_map.hpp:74:23: warning: ‘virtual const char* boost::dynamic_property_exception::what() const’ can be marked override [-Wsuggest-override]  
./boost/property_map/dynamic_property_map.hpp:83:15: warning: ‘virtual const char* boost::property_not_found::what() const’ can be marked override [-Wsuggest-override]  
./boost/property_map/dynamic_property_map.hpp:98:15: warning: ‘virtual const char* boost::dynamic_get_failure::what() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: jeking3  
> Created at: 2022-04-23 16:50:02 UTC  
> Url: https://github.com/boostorg/property_map/issues/14#issuecomment-1107533177  

This was fixed in d2a179c3.  It just hasn't made it to master yet.  I will get it there shortly.
