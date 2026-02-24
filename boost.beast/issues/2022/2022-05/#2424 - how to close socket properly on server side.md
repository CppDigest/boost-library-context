# #2424 - how to close socket properly on server side ?? [Closed]

> Username: RavikumarTulugu  
> Created at: 2022-05-11 12:04:59 UTC  
> Updated at: 2022-05-12 15:54:52 UTC  
> Closed at: 2022-05-12 05:23:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2424  

how to properly close connection on server side ??   
our server is an async ssl server.  our close logic is like below   
on receiving "closed" error in read handler, we post ( boost::asio::post )  an event to the io context and in the post handler we call ws.close(). close throws an exception and the server crashes.  The exception code is 125 ( operation cancelled ) .   
  
```c++  
close(close_reason const& cr)  
 {  
    static_assert(is_sync_stream<next_layer_type>::value,  
         "SyncStream type requirements not met");  
     error_code ec;  
     close(cr, ec);  
     if(ec)  
         BOOST_THROW_EXCEPTION(system_error{ec});   <---------- origin of exception.   
 }  
```

---

## Comment 1

> Username: sehe  
> Created at: 2022-05-11 13:19:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2424#issuecomment-1123752375  

What does "_on receiving "closed" error in read handler_" mean?  
  
In general `operation_aborted` means that an asynchronous operation was canceled. Cancellation implicitly happens when IO objects are destructed. I'd check the lifetime of the objects involved, including `ws`.  
  
Perhaps if you can make the reproducer self-contained, that would help. If you want you can base it on an existing example, like   
 https://www.boost.org/doc/libs/master/libs/beast/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp

---

## Comment 2

> Username: RavikumarTulugu  
> Created at: 2022-05-11 13:28:25 UTC  
> Updated at: 2022-05-11 13:31:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2424#issuecomment-1123763389  

sorry being vague, the error in our case we are relying on  to close the connection is "boost::beast::websocket::error::closed" , in this case the client is a browser tab and the event happens on the close of the tab. our code is in fact based on the async ssl server example. it is not clear to me as in why close is throwing exception.

---

## Comment 3

> Username: RavikumarTulugu  
> Created at: 2022-05-11 13:45:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2424#issuecomment-1123784006  

The async server ssl example just returns on this condition. code taken from beast example :   
if(ec == websocket::error::closed)  
            return;  
if we do the same in production, the destructor of the session never gets called and the socket connection remains half opened. netstat shows the connection as "CLOSE_WAIT" state. we are using boost 1.74 on ubuntu 22.04 , pls let us know if we have to move to new version.

---

## Comment 4

> Username: sehe  
> Created at: 2022-05-11 13:53:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2424#issuecomment-1123793362  

The example would not try to `close` the websocket after receiving `error::closed` (because of the `fail` in `on_read`).  
  
The server examples all handle `error::closed` without calling `close`, see e.g. https://www.boost.org/doc/libs/master/libs/beast/example/websocket/server/async-ssl/websocket_server_async_ssl.cpp, instead relying on destructors to do the right thing. Same for the chat-multi example.

---

## Comment 5

> Username: RavikumarTulugu  
> Created at: 2022-05-12 05:23:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2424#issuecomment-1124542250  

Returning from the read handler will automatically invoke the session destructor unless there are shared pointers floating around . Destructor will automatically close the network connection as well. The documentation and examples floating around are misleading , the latest beast library simplifies lot of things and reduces a lot of code. Our case was that a reference was floating around in a container leading to half closure of the socket.

---

## Comment 6

> Username: sehe  
> Created at: 2022-05-12 11:20:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2424#issuecomment-1124870314  

Glad you were able to get it sorted! Of you have suggestions on how to improve the examples (e.g. what specifically was misleading) we'd love to hear.  
  
Even if it is anti-patterns that are floating around in books/other websites, sometimes it can be worth calling it out with a warning in the Beast documentation.

---

## Comment 7

> Username: RavikumarTulugu  
> Created at: 2022-05-12 15:54:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2424#issuecomment-1125163249  

https://github.com/boostorg/beast/issues/1127  
I was totally misled by the above discussion , I was exactly facing the same issue .  This popped up in my google search. Also the code was pretty old and the api was changed and would not compile with the new library.
