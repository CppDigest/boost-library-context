# #349 - connection_pool: remove async_get_connection timeout overloads [Closed]

> Username: anarthal  
> Created at: 2024-09-21 19:12:24 UTC  
> Updated at: 2024-09-26 06:30:53 UTC  
> Closed at: 2024-09-26 06:30:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/349  

Now that we support per-operation cancellation and that timeouts have become trivial to implement using `asio::cancel_after`, it no longer makes sense having this feature built-in. Pools are experimental (until 1.87), so we should make the change now.
