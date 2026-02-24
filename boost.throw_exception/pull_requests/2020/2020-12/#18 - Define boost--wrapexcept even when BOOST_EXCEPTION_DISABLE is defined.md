# #18 Define boost::wrapexcept even when BOOST_EXCEPTION_DISABLE is defined. [Merged]

> Username: palebedev  
> Created at: 2020-12-17 22:58:19 UTC  
> Updated at: 2020-12-19 22:41:56 UTC  
> Merged at: 2020-12-19 13:50:16 UTC  
> Closed at: 2020-12-19 13:50:16 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/18  

Since Boost.Exception started using `boost::wrapexcept` in implementation of `boost::exception_ptr`, we need to define it even when `BOOST_EXCEPTION_DISABLE` is defined, otherwise it won't compile:  
  
```  
$ clang++ -DBOOST_EXCEPTION_DISABLE -w -x c++ /usr/include/boost/exception_ptr.hpp -fsyntax-only  
In file included from /usr/include/boost/exception_ptr.hpp:9:  
/usr/include/boost/exception/detail/exception_ptr.hpp:87:49: error: use of undeclared identifier 'wrapexcept'  
        return exception_ptr(boost::make_shared<wrapexcept<E> >(cp));  
                                                ^  
1 error generated.  
```

---
