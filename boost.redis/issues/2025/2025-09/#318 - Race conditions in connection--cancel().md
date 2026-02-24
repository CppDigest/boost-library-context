# #318 - Race conditions in connection::cancel() [Closed]

> Username: anarthal  
> Created at: 2025-09-27 16:45:10 UTC  
> Updated at: 2025-10-03 16:48:31 UTC  
> Closed at: 2025-10-03 16:48:31 UTC  
> Url: https://github.com/boostorg/redis/issues/318  

The cancellation mechanism in `connection::cancel()` is currently quite rudimentary, and subject to race conditions. If cancellation is called after an operation has completed, but before its handler is called, `connection::cancel()` does nothing. The correct way to handle this is using per-operation cancel.
