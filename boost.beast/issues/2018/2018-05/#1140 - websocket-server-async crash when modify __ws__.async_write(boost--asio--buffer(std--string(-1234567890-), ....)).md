# #1140 - websocket-server-async crash when modify __ws__.async_write(boost::asio::buffer(std::string("1234567890"), ....)) [Closed]

> Username: weiweiloong  
> Created at: 2018-05-23 16:10:31 UTC  
> Updated at: 2020-01-11 20:34:43 UTC  
> Closed at: 2018-05-29 03:06:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1140  

hello,  
       when l learn websocket how to use the demo of websocket-server-async;  
version:  
boost_1_66_0\boost\beast\websocket   
i only modeify __ws__.async_write(.........), and my code like this in the demo project "websocket-server-async";  
{  
。。。。。  
namespace websocket = boost::beast::websocket;  
    websocket::stream<tcp::socket> __ws__;  
    boost::asio::strand<boost::asio::io_context::executor_type> strand_;  
    boost::beast::multi_buffer buffer_;  
  
//__ws__.async_write( buffer_.data(), boost::asio::bind_executor( strand_, std::bind( &session::on_write, //shared_from_this(), std::placeholders::_1, std::placeholders::_2)));  
  
__ws__.async_write( boost::asio::buffer(std::string("1234567890")), boost::asio::bind_executor( strand_, std::bind( &session::on_write, shared_from_this(), std::placeholders::_1, std::placeholders::_2)));  
}  
but  the code is breakdown after i modify the code as  __ws__.async_write(boost::asio::buffer(std::string("1234567890"), ....)); when server recive information from websocket client;

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-23 17:39:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1140#issuecomment-391435692  

You are constructing the temporary string `std::string("1234567890")` and passing that as a parameter to `async_write`. When the string is destroyed, the asynchronous operation will produce an access violation. You need to make sure the string exists until at least the completion handler is invoked:  
```  
auto sp = std::make_shared<std::string>("1234567890");  
ws.async_write(  
    boost::asio::buffer(*sp), boost::asio::bind_executor(strand_,  
    [sp, self=shared_from_this()](boost::beast::error_code ec, std::size_t bytes_transferred)  
    {  
        self->on_write(ec, bytes_transferred);  
    }));  
```

---

## Comment 2

> Username: weiweiloong  
> Created at: 2018-05-28 05:02:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1140#issuecomment-392419708  

tks

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-05-28 20:31:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1140#issuecomment-392599014  

Has this issue been resolved?

---

## Comment 4

> Username: IESBMNT  
> Created at: 2019-10-24 01:19:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1140#issuecomment-545700210  

I’m doing something similar. I’ve adapted the example at  https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/server/async/websocket_server_async.cpp.  
  
I’m attempting to write asynchronously to websockets while they are open. In class Session I’ve added the following.  
```  
//Send a message to the client  
void Session::SendMessage(std::string& message)  
{  
    ws_.async_write(boost::asio::buffer(message), beast::bind_front_handler(&Session::OnWrite, writeString));  
   //Race condition if message is deallocated/reused/mutated  
}  
```  
  
How can I deallocate the message string on successful write? The provided Listener class uses .async_accept() to construct the Session, and does not seem to provide a way to reference the associated strand for use with bind_executor as in the answer above. With the current composed example I have no way to manage the lifetime of the message object, and it must be guaranteed to exist until write. Perhaps this is not idiomatic websocket use in beast?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-10-24 01:39:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1140#issuecomment-545704044  

Make a small reference counted, lightweight copyable container that meets the requirements of _ConstBufferSequence_ and pass that as the buffer. Something like this:  
https://github.com/vinniefalco/BeastLounge/blob/7be4bcf47c98c6bfd97ef3e0906c1dce0fd5ce75/server/message.hpp#L22

---

## Comment 6

> Username: IESBMNT  
> Created at: 2019-11-01 17:57:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1140#issuecomment-548888768  

That works, thank you.

---

## Comment 7

> Username: rraggerr  
> Created at: 2020-01-11 20:34:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1140#issuecomment-573352177  

thanks!
