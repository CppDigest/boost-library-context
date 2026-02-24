# #471 - All-uppercase capabilities might conflict with official C API macros [Closed]

> Username: anarthal  
> Created at: 2025-04-23 17:32:44 UTC  
> Updated at: 2025-04-25 18:11:20 UTC  
> Closed at: 2025-04-25 18:11:20 UTC  
> Url: https://github.com/boostorg/mysql/issues/471  

Capabilities defines constants like `CLIENT_LONG_PASSWORD`, which are equal to the ones in libmysqlclient. Although it's unlikely that the official client and Boost.MySQL are used together, we can do better. Also, `capabilities` could be a plain enum.
