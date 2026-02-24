# #381 Use declval in is_legacy_connect_condition check [Closed]

> Username: ecatmur  
> Created at: 2021-09-22 15:23:55 UTC  
> Updated at: 2021-09-22 15:24:44 UTC  
> Closed at: 2021-09-22 15:24:44 UTC  
> Url: https://github.com/boostorg/asio/pull/381  

gcc 11.2 w/ -O3 rejects:  
```  
error: 'this' pointer is null [-Werror=nonnull]  
   77 |         (*static_cast<legacy_connect_condition_helper<T, Iterator>*>(0))(  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
   78 |           *static_cast<const boost::system::error_code*>(0),  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   79 |           *static_cast<const Iterator*>(0)))) != 1;  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
The source code in this repository is generated from an upstream repository at https://github.com/chriskohlhoff/asio.  
  
Please consider raising new pull requests at https://github.com/chriskohlhoff/asio/pulls.

---

## Comment 1

> Username: ecatmur  
> Created_at: 2021-09-22 15:24:41 UTC  
> Url: https://github.com/boostorg/asio/pull/381#issuecomment-925036120  

ah, already opened at https://github.com/chriskohlhoff/asio/pull/882

---
