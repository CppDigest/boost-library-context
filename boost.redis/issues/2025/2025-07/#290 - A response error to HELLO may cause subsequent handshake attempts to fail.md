# #290 - A response error to HELLO may cause subsequent handshake attempts to fail [Closed]

> Username: anarthal  
> Created at: 2025-07-27 20:52:04 UTC  
> Updated at: 2025-09-04 14:48:02 UTC  
> Closed at: 2025-09-04 14:48:02 UTC  
> Url: https://github.com/boostorg/redis/issues/290  

If I'm reading [these lines](https://github.com/boostorg/redis/blob/88d8f3c0ca95701eb1f07e2ef11e808b84ce7251/include/boost/redis/detail/resp3_handshaker.hpp#L86) correctly, an error response to a HELLO (maybe due to a user that hasn't been created by a deployment pipeline yet) causes subsequent HELLO attempts to always error, since the error is never cleared.
