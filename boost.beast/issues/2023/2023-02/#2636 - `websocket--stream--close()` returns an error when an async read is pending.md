# #2636 - `websocket::stream::close()` returns an error when an async read is pending [Closed]

> Username: toughengineer  
> Created at: 2023-02-08 16:59:54 UTC  
> Updated at: 2023-02-08 19:16:07 UTC  
> Closed at: 2023-02-08 19:16:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2636  

When an async read operation (`websocket::stream::async_read()`) is pending, `websocket::stream::close()` returns an error:  
```  
End of file (asio.misc:2)  
```  
  
This reproduces if I modify the example server:  
https://www.boost.org/doc/libs/1_75_0/libs/beast/example/websocket/server/async/websocket_server_async.cpp  
  
```cpp  
void  
  on_read(  
    beast::error_code ec,  
    std::size_t bytes_transferred)  
{  
  boost::ignore_unused(bytes_transferred);  
  
  // This indicates that the session was closed  
  if (ec == websocket::error::closed)  
    return;  
  
  if(ec)  
    fail(ec, "read");  
  
  // send "disconnect" from a connected client  
  if (msg_ == "disconnect") {  
    // simulate closing from other thread  
    std::thread{[that = shared_from_this()] {  
      std::this_thread::sleep_for(std::chrono::seconds(1));  
      beast::error_code error;  
      that->ws_.close(websocket::close_code::normal, error);  
      if (error)  
        std::cerr << "Error on close: " << error.message() << " (" << error << ")\n";  
    }}.detach();  
  }  
  
  // Echo the message  
  ws_.text(ws_.got_text());  
  ws_.async_write(  
    buffer_.data(),  
    beast::bind_front_handler(  
      &session::on_write,  
      shared_from_this()));  
}  
```  
  
See attached [websocket_server_async.txt](https://github.com/boostorg/beast/files/10688048/websocket_server_async.txt).  
  
**Am I not supposed to use `close()` if an outstanding async read is pending?  
Nothing in the docs says I can't.**  
  
**What does the `End of file (asio.misc:2)` error mean in the case of `close()`?**  
  
Boost version: 1.75  
Platform: MSVC 2017/2022 on Windows

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-02-08 17:00:28 UTC  
> Updated at: 2023-02-08 17:01:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2636#issuecomment-1422945595  

You have to use `async_close` not `close`. And you cannot just call `async_close` from another thread, it has to be done from the same impliciit or explicit strand. `websocket::stream`, and I/O objects in general, are _not thread safe_. They cannot be accessed concurrently. Use `asio::post` on the websocket's executor to submit a lambda that calls `async_close` on the websocket stream.

---

## Comment 2

> Username: toughengineer  
> Created at: 2023-02-08 17:07:21 UTC  
> Updated at: 2023-02-08 17:09:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2636#issuecomment-1422956757  

@vinniefalco, thanks for lightning fast response!  
  
> You have to use `async_close` not `close`.  
  
Can you point me towards where this is discussed in the docs or some other place? Or is this folklore knowledge?  
  
> And you cannot just call `async_close` from another thread, it has to be done from the same implicit or explicit strand. `websocket::stream`, and I/O objects in general, are not thread safe. They cannot be accessed concurrently.  
  
This part I get. That was just a quick & dirty example/reproduction code.  
  
> Use `asio::post` on the websocket's executor to submit a lambda that calls `async_close` on the websocket stream.  
  
Seems like I can't synchronously close a websocket with pending async operations, huh?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-02-08 19:10:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2636#issuecomment-1423108691  

> Can you point me towards where this is discussed in the docs  
> or some other place? Or is this folklore knowledge?  
  
Well generally speaking you can't mix sync and async operations. Not sure where this is written up but its in there somewhere.  
  
> Seems like I can't synchronously close a websocket with pending async operations, huh?  
  
No, because "close" is a network operation. It involves sending a websocket frame, which means performing an asynchronous write, and these must be ordered correctly and meet the requirements for accessing objects concurrently.   
  
If you want to "just close the socket" you can do that instead, call `next_layer().close()` to close the TCP/IP socket directly. But this is not a Websocket protocol-level close.

---

## Comment 4

> Username: toughengineer  
> Created at: 2023-02-08 19:16:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2636#issuecomment-1423115563  

Got it. Thanks.  
  
> If you want to "just close the socket" you can do that instead, call `next_layer().close()` to close the TCP/IP socket directly. But this is not a Websocket protocol-level close.  
  
Yeah, in particular you can't specify close reason.
