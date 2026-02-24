# #20 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-13 01:52:54 UTC  
> Updated at: 2020-06-17 19:03:10 UTC  
> Closed at: 2020-06-17 19:03:10 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/20  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/numeric/conversion/converter_policies.hpp:140:26: warning: ‘virtual const char* boost::numeric::bad_numeric_cast::what() const’ can be marked override [-Wsuggest-override]  
./boost/numeric/conversion/converter_policies.hpp:148:26: warning: ‘virtual const char* boost::numeric::negative_overflow::what() const’ can be marked override [-Wsuggest-override]  
./boost/numeric/conversion/converter_policies.hpp:155:26: warning: ‘virtual const char* boost::numeric::positive_overflow::what() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-05-25 00:22:29 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/20#issuecomment-633322553  

See fixes in #21.

---

## Comment 2

> Username: EugeneZelenko  
> Created at: 2020-06-17 19:03:10 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/20#issuecomment-645563891  

Merged in #21.
