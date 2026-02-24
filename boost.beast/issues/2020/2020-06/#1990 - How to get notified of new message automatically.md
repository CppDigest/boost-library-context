# #1990 - How to get notified of new message automatically [Closed]

> Username: thomasdao  
> Created at: 2020-06-18 01:28:34 UTC  
> Updated at: 2020-06-21 18:11:48 UTC  
> Closed at: 2020-06-21 18:11:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1990  

Hi, I am implementing Web Socket from client, following the example `websocket_client_async_ssl_system_executor`, and I want to be automatically notified when new message is arrived from the server. I guess this is a common use case, but I search a lot of place and don't know what's the best way to do this.  
  
Currently, in `on_read` method, I am also add a call to `async_read`, so there's always a pending `async_read`. However I am struggling what to do when `read` failed. If I also call `async_read` when the previous `on_read` failed, it sometime ends up in infinite read failure. However if I don't call `async_read` when `on_read` failed, there's no more pending `async_read` and I don't know how to get notified of new message.  
  
```cpp  
    void  
    on_read(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if (ec) {  
             /* Should I call async_read when error happens?  
            ws_.async_read(  
                buffer_,  
                beast::bind_front_handler(  
                    &session::on_read,  
                    shared_from_this()));  
           */  
  
            return fail(ec, "read");  
        }  
  
        // Get data  
        if (ws_.got_text()) {  
            auto data = beast::buffers_to_string(buffer_.data());  
            if (auto cb = callback.lock()) {  
                cb->onMessage(data);  
            }  
        }  
  
        // Clear the buffer  
        buffer_.consume(buffer_.size());  
          
        // Keep reading message into our buffer  
        ws_.async_read(  
            buffer_,  
            beast::bind_front_handler(  
                &session::on_read,  
                shared_from_this()));  
    }  
```  
  
I am new to Beast so I'm quite sure I have missed something. If there's a proper way to do this in Beast? Is my approach above ok or should I call `async_read` periodically? Thank you.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-06-18 01:29:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1990#issuecomment-645714093  

Are you also new to Asio?

---

## Comment 2

> Username: thomasdao  
> Created at: 2020-06-18 01:29:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1990#issuecomment-645714250  

@vinniefalco yes I am new to Asio as well. Thank you for the super quick reply!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-06-18 01:31:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1990#issuecomment-645714641  

Are you new to C++?

---

## Comment 4

> Username: thomasdao  
> Created at: 2020-06-18 01:32:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1990#issuecomment-645715049  

@vinniefalco Yes I am new to C++ too. I'm sure this is probably a very beginner question :)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-06-18 01:33:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1990#issuecomment-645715248  

Well, to use Beast you are supposed to already know how to use Asio, and you are supposed to already have a firm grasp of C++ including template programming. That said, this example shows how to structure an "asynchronous read loop."  
https://github.com/boostorg/beast/blob/6f57e5934c1f307e4510ee11dd09594526a3f5d0/example/websocket/server/async/websocket_server_async.cpp#L107

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-06-18 12:05:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1990#issuecomment-645972678  

hi @thomasdao   
  
I don't know whether you have any experience with state machines. If so, it might be easier to visualise the use of a beast websocket as a state diagram:  
  
```puml  
state WebSocket {  
  
	[*] --> HandShaking  
    HandShaking --> Error : error  
    HandShaking --> Connected : success  
    state Connected {  
	    state fork_state <<fork>>  
    	[*] --> fork_state  
        fork_state --> Reading  
        fork_state --> Writing  
        fork_state --> Closing  
          
        state Reading {  
        	[*] --> WaitingRead : : async_read  
            WaitingRead --> HandlingFrame : success  
            WaitingRead --> [*] : read_error : process_event(error)  
            HandlingFrame --> WaitingRead : success  
            HandlingFrame --> [*] : error : process_event(error)  
              
        }  
          
        state Closing {  
        	[*] --> Idle  
            Idle --> WaitCloseRequest : close  
            WaitCloseRequest -> [*] : success  
            WaitCloseRequest -> [*] : close_error  
        }  
          
        state Writing {  
        
        	[*] --> WritingIdle  
            WritingIdle --> Sending : write : async_write  
            Sending : write : queue_pending_write  
            Sending --> Sending : success : async_write / pending_write  
            Sending --> WritingIdle : success / no_pending_write  
            Sending --> [*] : write_error  
        }  
          
          
        state join_state <<join>>  
        Reading --> join_state : read_error  
        Writing --> join_state : write_error  
        Closing --> join_state  
          
        join_state --> Error : read_error  
    }  
}  
```  
  
https://liveuml.com/view/5eeb5851663178088afef6e7

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-06-18 14:35:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1990#issuecomment-646059580  

If anyone reads this UML stuff and it helps them please let me know here.

---

## Comment 8

> Username: thomasdao  
> Created at: 2020-06-18 23:45:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1990#issuecomment-646359449  

Hi @madmongo1 and @vinniefalco, the UML is very useful, it's easier to understand the flow. It would be more complete if you include `async_resolve`, `async_connect`, `next_layer().async_handshake` and `ws_.async_handshake`. It takes me a bit of time to figure it out (but I'm really beginner) :).   
  
From the example @vinniefalco mentioned, I figured that in order to get notified when the message arrives, I need to call `do_read` in other callback to create async read loop. This is doable and I can get it working in my app. However coming from web, I wish it can just provide me a ready to use callback (with `onMessage`, `onError`, etc) similar to https://developer.mozilla.org/en-US/docs/Web/API/WebSocket, and not having to mess with the internal of websocket which I'm not really familiar.  
  
Thanks all for your help and thanks for the library!

---

## Comment 9

> Username: Yinpinghua  
> Created at: 2020-06-19 09:10:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1990#issuecomment-646526900  

加个定时器，避免僵尸连接

---

## Comment 10

> Username: madmongo1  
> Created at: 2020-06-21 18:11:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1990#issuecomment-647162142  

Question answered. Closing issue.
