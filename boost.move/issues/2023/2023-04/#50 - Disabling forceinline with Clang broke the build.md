# #50 - Disabling forceinline with Clang broke the build [Closed]

> Username: alandefreitas  
> Created at: 2023-04-19 00:30:07 UTC  
> Updated at: 2023-04-19 05:29:53 UTC  
> Closed at: 2023-04-19 05:19:08 UTC  
> Url: https://github.com/boostorg/move/issues/50  

https://github.com/alandefreitas/url/actions/runs/4737166802/jobs/8410131446 broke the build.  
  
Lots of errors like:  
  
```  
./boost/move/utility_core.hpp:290:7: error: 'BOOST_MOVE_FORCEINLINE' does not name a type  
       BOOST_MOVE_FORCEINLINE T&& move_if_not_lvalue_reference(typename ::boost::move_detail::remove_reference<T>::type& t) BOOST_NOEXCEPT  
       ^  
```  
  
It looks like the commit https://github.com/boostorg/move/commit/a6cdf134d09f705ba8762c7e9d5e2490cfdcce8d simply removed BOOST_MOVE_FORCEINLINE instead of defining it as empty but didn't stop using it in other files.  
  
Here's an example: https://github.com/alandefreitas/url/actions/runs/4737166802/jobs/8410131446

---

## Comment 1

> Username: igaztanaga  
> Created at: 2023-04-19 05:19:46 UTC  
> Url: https://github.com/boostorg/move/issues/50#issuecomment-1514147037  

It looks that my local regression didn't have clang enabled. Many thanks for the quick report.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2023-04-19 05:29:52 UTC  
> Url: https://github.com/boostorg/move/issues/50#issuecomment-1514153912  

It seems that regression tests in clang are working again:  
  
https://github.com/boostorg/move/actions/runs/4739821058/jobs/8415005405
