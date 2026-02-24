# #112 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-12 19:34:28 UTC  
> Updated at: 2021-02-04 23:17:26 UTC  
> Closed at: 2021-02-04 23:17:26 UTC  
> Url: https://github.com/boostorg/interprocess/issues/112  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/interprocess/exceptions.hpp:100:24: warning: ‘virtual const char* boost::interprocess::bad_alloc::what() const’ can be marked override [-Wsuggest-override]  
./boost/interprocess/exceptions.hpp:65:25: warning: ‘virtual const char* boost::interprocess::interprocess_exception::what() const’ can be marked override [-Wsuggest-override]  
./boost/interprocess/exceptions.hpp:89:24: warning: ‘virtual const char* boost::interprocess::lock_exception::what() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-04 23:17:26 UTC  
> Url: https://github.com/boostorg/interprocess/issues/112#issuecomment-773666492  

This was already fixed in Pull #117.
