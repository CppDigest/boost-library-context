# #256 - async_result for use_awaitable_t should expose completion_handler_type not handler_type [Closed]

> Username: cstratopoulos  
> Created at: 2019-06-27 19:12:59 UTC  
> Updated at: 2019-06-28 13:12:55 UTC  
> Closed at: 2019-06-28 13:12:55 UTC  
> Url: https://github.com/boostorg/asio/issues/256  

As per the [`async_result` docs](https://www.boost.org/doc/libs/1_70_0/doc/html/boost_asio/reference/async_result.html), specializations of `async_result` should expose `completion_handler_type`, but instead we currently have `handler_type`: https://github.com/boostorg/asio/blob/develop/include/boost/asio/impl/use_awaitable.hpp#L237

---

## Comment 1

> Username: djarek  
> Created at: 2019-06-27 21:33:21 UTC  
> Url: https://github.com/boostorg/asio/issues/256#issuecomment-506519969  

`completion_handler_type` is no longer required by the new form: https://github.com/boostorg/asio/commit/0568d3bf0b492887c6c48d05f5492a782dafa746
