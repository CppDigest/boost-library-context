# #296 - Make any_connection use asio::deferred as default [Closed]

> Username: anarthal  
> Created at: 2024-06-27 16:56:51 UTC  
> Updated at: 2024-08-13 08:48:51 UTC  
> Closed at: 2024-08-13 08:48:51 UTC  
> Url: https://github.com/boostorg/mysql/issues/296  

This would be consistent with [a recent change in Asio](https://github.com/chriskohlhoff/asio/commit/f4a831b85fdf5218a81601adddb32e9e20a07ddc) and simplify coroutine usages.
