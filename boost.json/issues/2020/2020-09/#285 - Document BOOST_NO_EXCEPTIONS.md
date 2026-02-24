# #285 - Document BOOST_NO_EXCEPTIONS [Closed]

> Username: vinniefalco  
> Created at: 2020-09-05 23:48:56 UTC  
> Updated at: 2020-09-06 10:41:20 UTC  
> Closed at: 2020-09-06 10:41:20 UTC  
> Url: https://github.com/boostorg/json/issues/285  

The overview should explain the use of this macro for Boost and for standalone

---

## Comment 1

> Username: pdimov  
> Created at: 2020-09-05 23:56:09 UTC  
> Url: https://github.com/boostorg/json/issues/285#issuecomment-687677680  

There's no "use" of this macro under the Boost configuration - it's automatically set by Boost.Config and automatically respected by boost::throw_exception.  
  
On standalone, the standard feature macro is `__cpp_exceptions`, I'm not sure how reliable it is. There are compiler-specific macros (from memory, `__EXCEPTIONS` on GCC, `_CPPUNWIND` on MSVC.)
