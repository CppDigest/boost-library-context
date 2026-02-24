# #27 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-12 19:01:00 UTC  
> Updated at: 2025-06-08 16:03:52 UTC  
> Closed at: 2025-06-08 16:03:51 UTC  
> Url: https://github.com/boostorg/contract/issues/27  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/contract/core/exception.hpp:107:25: warning: ‘virtual const char* boost::contract::bad_virtual_result_cast::what() const’ can be marked override [-Wsuggest-override]  
./boost/contract/core/exception.hpp:183:25: warning: ‘virtual const char* boost::contract::assertion_failure::what() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: lcaminiti  
> Created at: 2020-04-15 22:54:18 UTC  
> Url: https://github.com/boostorg/contract/issues/27#issuecomment-614319627  

This can be fixed using BOOST_OVERRIDE that should become part of Boost.Config from the next release: https://github.com/boostorg/config/pull/329.  
However, I will not generate a new release or Boost.Contract only to fix warnings. I will leave this issue open. If I have to generate a new Boost.Contract release in the future, I will then fix this warning as well.

---

## Comment 2

> Username: EugeneZelenko  
> Created at: 2020-05-23 00:59:09 UTC  
> Url: https://github.com/boostorg/contract/issues/27#issuecomment-632958825  

See fixes in #28.
