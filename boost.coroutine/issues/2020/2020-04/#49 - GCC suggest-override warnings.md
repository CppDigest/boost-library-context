# #49 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-12 19:05:04 UTC  
> Updated at: 2020-04-24 09:12:13 UTC  
> Closed at: 2020-04-24 09:12:13 UTC  
> Url: https://github.com/boostorg/coroutine/issues/49  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
libs/coroutine/src/exceptions.cpp:15:25: warning: ‘virtual const char* boost::coroutines::coroutine_error_category::name() const’ can be marked override [-Wsuggest-override]  
libs/coroutine/src/exceptions.cpp:18:25: warning: ‘virtual std::__cxx11::string boost::coroutines::coroutine_error_category::message(int) const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 09:12:13 UTC  
> Url: https://github.com/boostorg/coroutine/issues/49#issuecomment-618898765  

1. boost.coroutine is deprecated  
2. boost.coroutine is intended to be used with pre C++11 compilers (so aplling override is not recommended)
