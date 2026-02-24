# #18 - Closing a TLS stream should use async_shutdown [Closed]

> Username: anarthal  
> Created at: 2024-10-20 16:24:06 UTC  
> Updated at: 2025-01-31 09:32:06 UTC  
> Closed at: 2025-01-31 09:32:06 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/18  

Shutdown code for TLS should use `asio::ssl::async_shutdown`. Websocket shutdown should probably also use `async_close`.
