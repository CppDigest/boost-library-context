# #2965 - `keep_alive_pings` causes assertion failure [Closed]

> Username: ambbox  
> Created at: 2024-12-28 17:49:57 UTC  
> Updated at: 2025-01-05 12:20:09 UTC  
> Closed at: 2025-01-05 12:20:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2965  

I'm experiencing an assertion error from boost/beast/core/detail/stream_base.hpp:116 (`BOOST_ASSERT(! *b_);`). Comment says  
```  
            // If this assert goes off, it means you are attempting  
            // to issue two of the same asynchronous I/O operation  
            // at the same time, without waiting for the first one  
            // to complete. For example, attempting two simultaneous  
            // calls to async_read_some. Only one pending call of  
            // each I/O type (read and write) is permitted.  
```  
From the call stack, i can see it is coming from a write operation.  
![Image](https://github.com/user-attachments/assets/6809e573-987f-4383-80d7-206ef3ddcecd)  
  
  
The thing is, I never call any write operation directly, I only connect and read in a loop. I do, however, turn on the `keep_alive_pings` option (as suggested to me [here](https://github.com/boostorg/beast/issues/2964)). Without it, the error appears to not happen, but it is hard to say for sure because it sometimes happens only after several minutes. Could it be a bug in the `keep_alive_pings` option? I'm on boost version 1.86.0 (latest from conan).

---

## Comment 1

> Username: ashtum  
> Created at: 2024-12-28 17:53:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2965#issuecomment-2564390616  

You can have only one active `async_read` operation at a time. You must wait for the completion handler to be invoked before initiating another one. Does your code comply with this requirement?

---

## Comment 2

> Username: ambbox  
> Created at: 2024-12-28 18:03:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2965#issuecomment-2564392387  

1. My code complies with this requirement  
2. The error comes from a write operation

---

## Comment 3

> Username: ashtum  
> Created at: 2024-12-28 18:18:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2965#issuecomment-2564395407  

If you enable the `keep_alive_pings` option, `async_read` will perform write operations under the hood (for writing ping messages). However, it uses an internal soft mutex which allows initiating an `async_write` operation alongside it without any issue.  
Could you provide a minimal reproducible example that triggers that assertion?

---

## Comment 4

> Username: ambbox  
> Created at: 2024-12-28 22:57:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2965#issuecomment-2564546205  

Too hard to reproduce, or I have another issue. I'll let you know it I have something

---

## Comment 5

> Username: ambbox  
> Created at: 2025-01-03 19:17:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2965#issuecomment-2569702067  

I think the problem was calling `async_close` after the connection was already closed by the other side, or in parallel to getting `close` from the other side. Does it make sense that it would cause crashes? If so, how can I avoid that?

---

## Comment 6

> Username: ashtum  
> Created at: 2025-01-03 19:24:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2965#issuecomment-2569709185  

You can initiate an `async_close` operation while an `async_read` is active. The `async_read` will complete with [error::closed](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__error.html).    
However, if you're using a multi-threaded executor, you need to ensure that the operation is invoked from the same strand as the one used by `websocket::stream`.

---

## Comment 7

> Username: ambbox  
> Created at: 2025-01-05 12:20:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2965#issuecomment-2571606945  

After reading the [`async_close`'s documentation](https://live.boost.org/doc/libs/1_86_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html), my problem was probably that i didn't continue to read after calling it
