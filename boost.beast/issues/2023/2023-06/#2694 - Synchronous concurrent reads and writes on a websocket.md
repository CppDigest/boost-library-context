# #2694 - Synchronous concurrent reads and writes on a websocket? [Closed]

> Username: lava  
> Created at: 2023-06-01 12:20:57 UTC  
> Updated at: 2023-06-28 14:54:45 UTC  
> Closed at: 2023-06-28 14:54:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2694  

I'd like to use a websocket connection where one thread is continuously reading, and another thread is writing messages (and pings) as they arrive.  
  
As far as I can tell, this is not possible to implement with synchronous operations, since it is not allowed to call the `read()` and `write()` member functions of a stream object concurrently from different threads, and once we're in a synchronous `read()` there's no way to cancel or interrupt it. For `wss` sockets it's also likely to stay that way because the underlying OpenSSL object also doesn't allow concurrent calls to `SSL_read()` and `SSL_write()`. (for plain websockets it seems like the underlying TCP socket should support this, but I can't tell if ASIO and/or beast does)  
  
However all the existing discussion I could find only talks about async reads and writes, so I'd like to sanity check: Is my understanding correct on this?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-06-01 13:15:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2694#issuecomment-1572034763  

Yes, your understanding is correct.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-06-07 05:36:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2694#issuecomment-1579934330  

Is this issue still needed?

---

## Comment 3

> Username: lava  
> Created at: 2023-06-28 14:54:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2694#issuecomment-1611597597  

No, all good, thanks for the reply.
