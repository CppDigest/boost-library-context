# #135 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-12 19:22:44 UTC  
> Updated at: 2020-05-05 13:36:53 UTC  
> Closed at: 2020-05-05 11:56:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/135  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/filesystem/exception.hpp:72:15: warning: ‘virtual const char* boost::filesystem::filesystem_error::what() const’ can be marked override [-Wsuggest-override]  
libs/filesystem/src/codecvt_error_category.cpp:34:19: warning: ‘virtual const char* {anonymous}::codecvt_error_cat::name() const’ can be marked override [-Wsuggest-override]  
libs/filesystem/src/codecvt_error_category.cpp:35:20: warning: ‘virtual std::__cxx11::string {anonymous}::codecvt_error_cat::message(int) const’ can be marked override [-Wsuggest-override]  
</pre>  
  
Related: boostorg/config#253

---

## Comment 1

> Username: Lastique  
> Created at: 2020-04-12 22:02:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/135#issuecomment-612682762  

https://github.com/boostorg/config/pull/329 needs to be merged first.

---

## Comment 2

> Username: EugeneZelenko  
> Created at: 2020-04-13 18:02:55 UTC  
> Url: https://github.com/boostorg/filesystem/issues/135#issuecomment-613017608  

boostorg/config#329 was merged. See also  boostorg/wave#88 for Clang-tidy command line to fix warnings.

---

## Comment 3

> Username: EugeneZelenko  
> Created at: 2020-05-05 13:36:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/135#issuecomment-624059786  

Thank you for help!
