# #659 - http read_some documentation and return value [Closed]

> Username: vinniefalco  
> Created at: 2017-07-16 14:55:36 UTC  
> Updated at: 2017-07-18 23:57:58 UTC  
> Closed at: 2017-07-18 23:57:58 UTC  
> Url: https://github.com/boostorg/beast/issues/659  

> the documentation for these functions still refers in places to a return value (or additional size_t argument to the completion handler), which is no longer in the code: synchronous read_some has a void return and async_read_some has a void(error_code) handler. Although this is probably vestigial documentation, actually I would prefer it if these lower level operations *did* return something, like the number of bytes transferred in the operation. This would assist in detecting badly behaved or pathological clients (like ones doing everything one byte at a time), and this is probably the main reason I would consider using the layer 2 operations.
