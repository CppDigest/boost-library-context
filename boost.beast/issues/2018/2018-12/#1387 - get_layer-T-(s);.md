# #1387 - get_layer<T>(s); [Closed]

> Username: vinniefalco  
> Created at: 2018-12-23 05:19:04 UTC  
> Updated at: 2020-03-12 17:26:32 UTC  
> Closed at: 2020-03-12 17:26:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1387  

The system of layering I/O objects, for example with `next_layer()` and `lowest_layer()` could use some formalization in the way of one or more named requirements. We can also have a utility function `get_layer<>` which returns a particularly typed layer.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-03-12 17:26:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1387#issuecomment-598314424  

This is too try-hard. Getting the lowest layer is good enough, and we have `beast::get_lowest_layer` for that.
