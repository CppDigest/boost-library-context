# #412 - Race condition in the sync connection_pool snippet [Closed]

> Username: anarthal  
> Created at: 2025-02-05 17:01:40 UTC  
> Updated at: 2025-02-14 13:03:05 UTC  
> Closed at: 2025-02-14 13:03:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/412  

There is a race condition in the code snippet showing how to use `asio::use_future`, due to `asio::cancel_after` being called from the main thread. The snippet should probably be an example, instead.
