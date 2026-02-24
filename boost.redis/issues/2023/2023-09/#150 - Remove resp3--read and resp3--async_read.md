# #150 - Remove resp3::read and resp3::async_read [Closed]

> Username: mzimbres  
> Created at: 2023-09-01 18:03:38 UTC  
> Updated at: 2023-09-02 12:52:38 UTC  
> Closed at: 2023-09-02 12:52:38 UTC  
> Url: https://github.com/boostorg/redis/issues/150  

These functions are not used anymore after the event multiplexer has been reformulated in terms of `socket.async_read_some`.
