# #2327 - Async server can't write long strings [Closed]

> Username: fechan  
> Created at: 2021-10-10 07:15:51 UTC  
> Updated at: 2021-10-11 18:56:47 UTC  
> Closed at: 2021-10-11 18:56:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2327  

Beast version: 318  
Compiler: g++ 11.1.0 on Arch Linux x86_64  
  
I'm trying to modify the async websocket server example from https://github.com/boostorg/beast/blob/b15a5ff0e47e72ba3d4711d2514bc65749d036ae/example/websocket/server/async/websocket_server_async.cpp to send a simple string of various lengths to the client, instead of echoing the client's message back to them. I'm running into a problem where if I try to send a string that is too long (e.g. 983039 characters), the server will fail to write the string through the websocket and "write: bad address" is printed to cerr. Short strings send just fine (e.g. 200 characters).  
  
My only change to the example is to the `on_read` function of the `session` class, which is as follows. Any help is appreciated, and thanks in advance!  
  
```cpp  
    void  
    on_read(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This indicates that the session was closed  
        if(ec == websocket::error::closed)  
            return;  
  
        if(ec)  
            fail(ec, "read");  
  
        std::string wsResponse = "";  
        for (int i = 0; i < 983039; i++) {  
            wsResponse += "a";  
        }  
        net::const_buffer responseBuffer = net::buffer(wsResponse);  
  
        // (don't) Echo the message  
        ws_.text(ws_.got_text());  
        ws_.async_write(  
            responseBuffer, // (try to send the buffer with the string we constructed)  
            beast::bind_front_handler(  
                &session::on_write,  
                shared_from_this()));  
    }  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-10-10 09:23:02 UTC  
> Updated at: 2021-10-10 09:24:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2327#issuecomment-939437877  

Every asynchronous operation requires that the lifetime of all buffers supplied in the initiating function must not end prior to the completion of the operation. That is, until the completion handler has been executed.  
  
In your case you are allocating a string buffer in the function's stack frame:  
```  
 std::string wsResponse = "";  
```  
Which then goes out of scope at the end of the function, after you have initiated the write:  
```  
        ws_.async_write(  
            responseBuffer, // (try to send the buffer with the string we constructed)  
            beast::bind_front_handler(  
                &session::on_write,  
                shared_from_this()));  
        // function return is here. wsResponse goes out of scope, but the async write is in progress. UB ensues.  
```  
  
To fix this you must extend the lifetime of the response buffer. Here is one way:  
  
```  
        auto pResponse = std::make_shared<std::string>();  
        for (int i = 0; i < 983039; i++) {  
            *pResponse += "a";  
        }  
        net::const_buffer responseBuffer = net::buffer(*pResponse);  
  
        // (don't) Echo the message  
        ws_.text(ws_.got_text());  
        ws_.async_write(  
            responseBuffer, // (try to send the buffer with the string we constructed)  
            [self = shared_from_this(), pResponse /* captured! */](error_code ec, std::size_t n  
            {  
              self->on_write(ec, n);  
            });  
```  
  
Note that in this case we don't really need the shared_ptr - a unique_ptr will do since the completion handler is moveable:  
```  
        auto pResponse = std::make_unique<std::string>();  
        for (int i = 0; i < 983039; i++) {  
            *pResponse += "a";  
        }  
        net::const_buffer responseBuffer = net::buffer(*pResponse);  
  
        // (don't) Echo the message  
        ws_.text(ws_.got_text());  
        ws_.async_write(  
            responseBuffer, // (try to send the buffer with the string we constructed)  
            [self = shared_from_this(), capture = std::move(pResponse) /* captured! */](error_code ec, std::size_t n  
            {  
              self->on_write(ec, n);  
            });  
```  
  
This works because the `responseBuffer` is effectively a reference in to the std::string, whos address is stable.  
  
Another way to ensure the lifetime of the response string is to embed it in the connection class - this has the benefit that you can re-use it without further memory allocations on subsequent writes.  
  
For the sake of completeness, when using coroutines, the buffer lifetime issues are easier to handle, eg:  
  
```  
    asio::awaitable<void>  
    respond()  
    {  
        std::string wsResponse = "";  // allocated in the coroutine frame  
        for (int i = 0; i < 983039; i++) {  
            wsResponse += "a";  
        }  
        net::const_buffer responseBuffer = net::buffer(wsResponse);  
  
        // (don't) Echo the message  
        ws_.text(ws_.got_text());  
  
        co_await ws_.async_write(   // this is now a suspension point  
            net::buffer(wsResponse),  
            asio::use_awaitable);  // the use_awaitable completion token turns the initiating function into a coroutine  
  
        // because this is a coroutine, the async_wait has now completed, and wsResponse is still alive in the coroutine frame.  
    }  
```

---

## Comment 2

> Username: fechan  
> Created at: 2021-10-11 18:56:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2327#issuecomment-940363189  

Thanks for the detailed response! Your explanation was very helpful and I've managed to get it working.
