# #65 - Move redis::resp3::async_read to redis:: namespace [Closed]

> Username: mzimbres  
> Created at: 2023-02-11 14:04:38 UTC  
> Updated at: 2023-02-12 06:44:34 UTC  
> Closed at: 2023-02-12 06:44:34 UTC  
> Url: https://github.com/boostorg/redis/issues/65  

The `async_read` function should be in the same namespace as`redis::connection`. With that change the resp3 module will be IO agnostic or "sans IO".
