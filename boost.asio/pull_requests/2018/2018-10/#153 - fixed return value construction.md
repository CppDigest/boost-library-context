# #153 fixed return value construction [Closed]

> Username: bebuch  
> Created at: 2018-10-15 11:30:51 UTC  
> Updated at: 2020-12-30 02:03:26 UTC  
> Closed at: 2020-12-30 02:03:26 UTC  
> Url: https://github.com/boostorg/asio/pull/153  

Warning from clang 7:  
  
```  
$(BOOST_ROOT)/boost/asio/ip/basic_resolver_results.hpp:256:12: warning: local variable 'tmp' will be copied despite being returned by name [-Wreturn-std-move]  
    return tmp;  
           ^~~  
[...]  
$(BOOST_ROOT)/boost/asio/ip/basic_resolver_results.hpp:256:12: note: call 'std::move' explicitly to avoid copying  
    return tmp;  
           ^~~  
           std::move(tmp)  
```

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2020-12-30 02:03:26 UTC  
> Url: https://github.com/boostorg/asio/pull/153#issuecomment-752300986  

Fixed in asio 1.12.2 / boost 1.69.

---
