# #2964 - How to detect connection loss for websocket? [Closed]

> Username: ambbox  
> Created at: 2024-12-25 15:49:30 UTC  
> Updated at: 2024-12-25 17:41:29 UTC  
> Closed at: 2024-12-25 17:41:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2964  

As a test i turn off the internet while connected. read requests just wait. ping and pong return without error.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-12-25 16:36:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2964#issuecomment-2561946972  

[websocket::stream::async_read](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read.html) will complete with an error if a ping/pong operation times out. However, automatic ping/pong operations are disabled by default. To enable them, you must configure the timeout options as follows:  
  
```cpp  
websocket::stream_base::timeout opt;  
opt.handshake_timeout = std::chrono::seconds(30);  
opt.idle_timeout      = std::chrono::seconds(30);  
opt.keep_alive_pings  = true;  
ws.set_option(opt);  
```

---

## Comment 2

> Username: ambbox  
> Created at: 2024-12-25 17:17:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2964#issuecomment-2561956444  

What about non-automatic ping/pong? I'm calling [ping](https://live.boost.org/doc/libs/1_87_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/ping.html)/[pong](https://live.boost.org/doc/libs/1_87_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/pong.html) directly and getting success. Write operations also succeed. Again, there is no interned connection. Is this normal?

---

## Comment 3

> Username: ashtum  
> Created at: 2024-12-25 17:24:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2964#issuecomment-2561957880  

Yes, the `write` operation succeeds because it writes data into the TCP buffer in the OS, even as it attempts to send it to the peer. This is why ping/pong operations are necessary. You can confirm the connection is active only if you receive a pong response.
