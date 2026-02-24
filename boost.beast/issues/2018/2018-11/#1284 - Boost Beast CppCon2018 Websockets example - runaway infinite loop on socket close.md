# #1284 - Boost Beast CppCon2018 Websockets example - runaway infinite loop on socket close [Closed]

> Username: forresttales  
> Created at: 2018-11-01 02:39:02 UTC  
> Updated at: 2018-11-04 01:57:51 UTC  
> Closed at: 2018-11-04 01:57:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1284  

Vinnie,  
Per the example here,  
https://github.com/vinniefalco/CppCon2018/blob/master/websocket_session.cpp  
```  
void  
websocket_session::  
on_read(error_code ec, std::size_t)  
{  
    // Handle the error, if any  
    if(ec)  
        fail(ec, "read");  
  
    // Send to all connections  
    state_->send(beast::buffers_to_string(buffer_.data()));  
  
    // Clear the buffer  
    buffer_.consume(buffer_.size());  
  
    // Read another message  
    ws_.async_read(  
        buffer_,  
        [sp = shared_from_this()](  
            error_code ec, std::size_t bytes)  
        {  
            sp->on_read(ec, bytes);  
        });  
}   
```  
  
From the browser, upon 'Disconnect' or closing the web page, the above will toggle, quickly eating up memory and cpu.   
websocket_session::on_read  
websocket_session::fail - read: Operation canceled  
websocket_session send  
websocket_session::on_read  
websocket_session::fail - read: Operation canceled  
websocket_session send  
websocket_session::on_read  
  
As the other functions have it, it looks like you just forgot the "return" ...  
```  
    // Handle the error, if any  
    if(ec)  
        return fail(ec, "read");  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-01 03:02:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1284#issuecomment-434915306  

You're right!! Also this should have gone into the other repo but no prob I will link it. Thanks!

---

## Comment 2

> Username: forresttales  
> Created at: 2018-11-01 03:03:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1284#issuecomment-434915503  

i thought about that. But the other repo has 0 issues. I didn't want to be the "1" ))

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-11-01 03:10:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1284#issuecomment-434916569  

ROFL... I feel you
