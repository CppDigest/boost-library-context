# #351 - connection_pool: remove detail::run_with_timeout [Closed]

> Username: anarthal  
> Created at: 2024-09-21 19:14:05 UTC  
> Updated at: 2024-10-04 09:35:03 UTC  
> Closed at: 2024-10-04 09:35:03 UTC  
> Url: https://github.com/boostorg/mysql/issues/351  

This can probably be replaced by `asio::cancel_after`.
