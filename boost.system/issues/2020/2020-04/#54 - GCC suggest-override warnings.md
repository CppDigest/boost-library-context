# #54 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-13 01:55:17 UTC  
> Updated at: 2020-06-19 16:22:32 UTC  
> Closed at: 2020-06-19 16:22:32 UTC  
> Url: https://github.com/boostorg/system/issues/54  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`. Duplicated warnings from same location are omitted:  
  
<pre>  
./boost/system/detail/std_interoperability.hpp:50:26: warning: ‘virtual const char* boost::system::detail::std_category::name() const’ can be marked override [-Wsuggest-override]  
./boost/system/detail/std_interoperability.hpp:55:25: warning: ‘virtual std::__cxx11::string boost::system::detail::std_category::message(int) const’ can be marked override [-Wsuggest-override]  
./boost/system/detail/std_interoperability.hpp:60:34: warning: ‘virtual std::error_condition boost::system::detail::std_category::default_error_condition(int) const’ can be marked override [-Wsuggest-override]  
./boost/system/detail/std_interoperability.hpp:65:18: warning: ‘virtual bool boost::system::detail::std_category::equivalent(int, const std::error_condition&) const’ can be marked override [-Wsuggest-override]  
./boost/system/detail/std_interoperability.hpp:66:18: warning: ‘virtual bool boost::system::detail::std_category::equivalent(const std::error_code&, int) const’ can be marked override [-Wsuggest-override]  
./boost/system/error_code.hpp:290:18: warning: ‘virtual const char* boost::system::detail::generic_error_category::name() const’ can be marked override [-Wsuggest-override]  
./boost/system/error_code.hpp:295:17: warning: ‘virtual std::__cxx11::string boost::system::detail::generic_error_category::message(int) const’ can be marked override [-Wsuggest-override]  
./boost/system/error_code.hpp:296:18: warning: ‘virtual const char* boost::system::detail::generic_error_category::message(int, char*, std::size_t) const’ can be marked override [-Wsuggest-override]  
./boost/system/error_code.hpp:308:18: warning: ‘virtual const char* boost::system::detail::system_error_category::name() const’ can be marked override [-Wsuggest-override]  
./boost/system/error_code.hpp:313:21: warning: ‘virtual boost::system::error_condition boost::system::detail::system_error_category::default_error_condition(int) const’ can be marked override [-Wsuggest-override]  
./boost/system/error_code.hpp:315:17: warning: ‘virtual std::__cxx11::string boost::system::detail::system_error_category::message(int) const’ can be marked override [-Wsuggest-override]  
./boost/system/error_code.hpp:316:18: warning: ‘virtual const char* boost::system::detail::system_error_category::message(int, char*, std::size_t) const’ can be marked override [-Wsuggest-override]  
./boost/system/system_error.hpp:50:20: warning: ‘virtual const char* boost::system::system_error::what() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-05-25 01:50:47 UTC  
> Url: https://github.com/boostorg/system/issues/54#issuecomment-633338548  

See fixes in #55.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-06-19 16:22:32 UTC  
> Url: https://github.com/boostorg/system/issues/54#issuecomment-646728417  

Should be fixed in 73bf30ae047268403a772e194e9c6d3f53d787ed and f00c76a3fcb34827a1830b9b784142ee9d1156f9.
