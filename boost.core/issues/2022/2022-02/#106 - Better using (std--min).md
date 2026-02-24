# #106 - Better using (std::min) [Closed]

> Username: UMU618  
> Created at: 2022-02-11 05:25:27 UTC  
> Updated at: 2022-02-11 05:29:03 UTC  
> Closed at: 2022-02-11 05:29:03 UTC  
> Url: https://github.com/boostorg/core/issues/106  

https://github.com/boostorg/core/blob/a1ad1784bfc21133f4d83fbf47dda961bb4a7c31/include/boost/core/detail/string_view.hpp#L541  
  
When I compile Boost.URL with Boost 1.78, it just can't compile because of std::min.

---

## Comment 1

> Username: UMU618  
> Created at: 2022-02-11 05:29:03 UTC  
> Url: https://github.com/boostorg/core/issues/106#issuecomment-1035895205  

[[#https://github.com/boostorg/core/commit/df3b9827cfc9f38080c8d90af048f1f20c449c36](https://github.com/boostorg/core/commit/df3b9827cfc9f38080c8d90af048f1f20c449c36)](https://github.com/boostorg/core/commit/df3b9827cfc9f38080c8d90af048f1f20c449c36)
