# #13 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-12 18:38:58 UTC  
> Updated at: 2020-05-03 14:25:41 UTC  
> Closed at: 2020-05-03 13:20:10 UTC  
> Url: https://github.com/boostorg/any/issues/13  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`. Duplicated warnings from same location are omitted::  
  
<pre>  
./boost/any.hpp:190:56: warning: ‘const type_info& boost::any::holder<ValueType>::type() const [with ValueType = bool; boost::typeindex::type_info = std::type_info]’ can be marked override [-Wsuggest-override]  
./boost/any.hpp:195:35: warning: ‘boost::any::placeholder* boost::any::holder<ValueType>::clone() const [with ValueType = bool]’ can be marked override [-Wsuggest-override]  
./boost/any.hpp:241:30: warning: ‘virtual const char* boost::bad_any_cast::what() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-05-03 13:20:17 UTC  
> Url: https://github.com/boostorg/any/issues/13#issuecomment-623109279  

Thanks for the report!

---

## Comment 2

> Username: EugeneZelenko  
> Created at: 2020-05-03 14:25:40 UTC  
> Url: https://github.com/boostorg/any/issues/13#issuecomment-623118176  

Thank you for fix!
