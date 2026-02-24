# #189 - any_connection: support caching_sha2_password with UNIX connections [Closed]

> Username: anarthal  
> Created at: 2023-12-19 12:01:37 UTC  
> Updated at: 2024-03-05 11:54:19 UTC  
> Closed at: 2024-03-05 11:54:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/189  

UNIX connections disable SSL, and this makes handshake reject caching_sha2_password connections. But UNIX sockets are considered secure channels.
