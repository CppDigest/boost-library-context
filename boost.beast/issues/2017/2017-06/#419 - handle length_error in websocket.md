# #419 - handle length_error in websocket [Closed]

> Username: vinniefalco  
> Created at: 2017-06-04 02:37:07 UTC  
> Updated at: 2017-08-04 23:09:05 UTC  
> Closed at: 2017-08-04 23:09:05 UTC  
> Url: https://github.com/boostorg/beast/issues/419  

When a dynamic buffer throws `std::length_error` on `prepare`, the implementation should catch it and covert it to `websocket::error::message_too_large`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-04 23:09:05 UTC  
> Url: https://github.com/boostorg/beast/issues/419#issuecomment-320371983  

Done
