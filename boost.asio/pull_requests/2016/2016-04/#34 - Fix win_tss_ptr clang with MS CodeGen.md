# #34 Fix win_tss_ptr clang with MS CodeGen [Closed]

> Username: vinniefalco  
> Created at: 2016-04-19 16:13:16 UTC  
> Updated at: 2017-12-02 07:45:19 UTC  
> Closed at: 2017-12-02 07:45:19 UTC  
> Url: https://github.com/boostorg/asio/pull/34  

This fixes the following error when building using clang with MS CodeGen in Visual Studio:  
  
```  
boost/asio/detail/impl/win_tss_ptr.ipp(37,27): error : enumerator value evaluates to  
4294967295, which cannot be narrowed to type 'int' [-Wc++11-narrowing]  
```

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:45:19 UTC  
> Url: https://github.com/boostorg/asio/pull/34#issuecomment-348675374  

Fixed in develop.

---
