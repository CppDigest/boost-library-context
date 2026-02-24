# #182 Fix spurious "success" on SSL system errors: [Closed]

> Username: vinniefalco  
> Created at: 2018-12-25 17:23:51 UTC  
> Updated at: 2019-03-31 17:00:12 UTC  
> Closed at: 2019-03-31 17:00:12 UTC  
> Url: https://github.com/boostorg/asio/pull/182  

Fix boostorg/beast#807  
Fix boostorg/beast#1373  
  
This resolves an issue where an ssl::stream read operation  
returns a successful error code but zero bytes transferred,  
violating its contract.  
  
* Treat SSL_ERROR_ZERO_RETURN as EOF  
  
* Use the ssl category for sys_error  
  
* On error, if sys_error is 0 (success),  
  return ssl::errors::read_sys_error instead.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2019-03-31 17:00:12 UTC  
> Url: https://github.com/boostorg/asio/pull/182#issuecomment-478358975  

Fixed here; https://github.com/boostorg/asio/commit/ce7e3bbf4b7070b8292df50d3514c34ce0353684

---
