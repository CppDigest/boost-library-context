# #1359 - Handle operations being abandoned in timeout_socket [Closed]

> Username: vinniefalco  
> Created at: 2018-12-08 21:10:35 UTC  
> Updated at: 2019-01-13 01:54:55 UTC  
> Closed at: 2019-01-13 01:54:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1359  

The saved handlers need to be destroyed if the timeout handler is destroyed without being invoked.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-01-13 01:54:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1359#issuecomment-453795345  

no longer relevant, as `saved_handler` is not used in the implementation any more
