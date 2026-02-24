# #378 - Simplify websocket::stream::read_op [Closed]

> Username: vinniefalco  
> Created at: 2017-05-23 14:37:51 UTC  
> Updated at: 2017-06-12 11:05:51 UTC  
> Closed at: 2017-06-12 11:05:51 UTC  
> Url: https://github.com/boostorg/beast/issues/378  

This composed operation can be implemented using a copyable class ("stack ripping") instead of parking the state in a `handler_ptr`. This could eliminate some memory allocations.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-12 11:05:51 UTC  
> Url: https://github.com/boostorg/beast/issues/378#issuecomment-307758875  

Done
