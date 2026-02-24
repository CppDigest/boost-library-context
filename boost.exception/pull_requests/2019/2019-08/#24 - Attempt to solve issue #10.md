# #24  Attempt to solve issue #10 [Merged]

> Username: dmenendez-gruposantander  
> Created at: 2019-08-27 12:38:51 UTC  
> Updated at: 2020-01-29 21:41:49 UTC  
> Merged at: 2020-01-29 21:41:49 UTC  
> Closed at: 2020-01-29 21:41:49 UTC  
> Url: https://github.com/boostorg/exception/pull/24  

boost::current_exception uses C++11 std::current_exception when supported to transport exceptions without the need to wrap in boost::enable_current_exception.

---

## Comment 1

> Username: dmenendez-gruposantander  
> Created_at: 2019-08-28 08:45:23 UTC  
> Url: https://github.com/boostorg/exception/pull/24#issuecomment-525646081  

gcc errors: some versions are complaining with   
`./boost/exception/detail/exception_ptr.hpp:436:90: error: cannot allocate an object of abstract type boost::exception_detail::clone_base`  
but the offending code does not in fact instantiate clone_base!  
```  
exception_detail::clone_base const& base {  
     boost::enable_current_exception(std::current_exception())};  
return exception_ptr(shared_ptr<exception_detail::clone_base const>(base.clone()));  
```  
I'm declaring base as a reference to the base class to bind the temporary of whatever class is returned from `boost::enable_current_exception()`  
Maybe I should just be using `auto`?

---

## Comment 2

> Username: dmenendez-gruposantander  
> Created_at: 2019-08-28 08:50:57 UTC  
> Url: https://github.com/boostorg/exception/pull/24#issuecomment-525648126  

clang errors: I don't see how the reported error is even remotely related to my changes...  
```  
bin.v2/libs/exception/test/cloning_test.test/clang-linux-3.6.2/debug/link-static/threading-multi/visibility-hidden/cloning_test.o: In function `void test_std_exception_what<std::ios_base::failure>()':  
/home/travis/build/boostorg/boost-root/libs/exception/test/cloning_test.cpp:168: undefined reference to `std::ios_base::failure::failure(char const*, std::error_code const&)'  
```

---

## Comment 3

> Username: dmenendez-gruposantander  
> Created_at: 2019-10-18 11:11:49 UTC  
> Url: https://github.com/boostorg/exception/pull/24#issuecomment-543679510  

Clang errors are gone after adding `dist: trusty` to the Travis config.  
  
However the gcc errors for older versions of gcc are still there!  :(

---
