# #2519 - Sending message right before closing websocket [Closed]

> Username: rdzienis  
> Created at: 2022-09-12 10:53:43 UTC  
> Updated at: 2024-01-03 08:57:11 UTC  
> Closed at: 2024-01-03 08:57:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2519  

I have an issue with letting the client know about disconnection purpose.   
I wanted to use deadline timer to have a timeout on async_read operation. If the client is inactive for 30 seconds, we send him message with such info and THEN disconnect.  
  
Part of my code:  
  
`boost::asio::yield_context yield`  
`boost::beast::flat_buffer buffer;`  
`boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>>* ws`  
`boost::asio::deadline_timer t(ioc, boost::posix_time::seconds(session.timeout));`  
`t.async_wait(boost::bind(&Connection::setInactivity, this, boost::asio::placeholders::error()));`  
`(*ws).async_read(buffer, yield[ec]);`  
  
setInactivity method is just setting session.active to false.  
then I perform :  
`boost::beast::multi_buffer resp;  
    boost::system::error_code ec;`  
  
    `(*ws).text((*ws).got_text());  
    size_t n = buffer_copy(resp.prepare(response.size()), boost::asio::buffer(response));  
    resp.commit(n);  
  
    (*ws).async_write(resp.data(), yield[ec]);  
    if (ec)  
    {  
  
        return showErrorMessage(ec, "method: async_write");  
    }  
    else  
    {  
        PRINT_INFO("Request processed - sent response");  
    }`  
  
It looked just fine, but when I called async_write, I got the following error:  
**Operation canceled**  
  
Is it even possible to send a message before disconnecting?  
  
_Note: I found it impossible with built-in timeout which disconnects automatically._

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-09-12 11:02:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1243571160  

definitely don't use beast::tcp_stream's internal timeout with websocket.   
  
As I read it, your `t.async_wait(...)` code won't execute until after the async_read has completed, because you are yielding the coroutine.  
  
It might be a good idea if you can link a github repo containing a complete, small, self-contained program that demonstrates what you are trying to do. I can then offer suggestions on how to correct it.

---

## Comment 2

> Username: rdzienis  
> Created at: 2022-09-12 11:06:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1243576716  

I guess it will be hard to link a github repo as it is confidential.  
I pasted the code in wrong order, sorry.  
Firsty I start the timer, then I call async_read.  
  
As far as I understand, I'm not "closing" the async_read, so I can't perform async_write.. but I don't know how to close/end/stop async_read.

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-09-12 11:08:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1243579795  

Don't copy your company's code. Write a new, very small, program which demonstrates the principle of what you want to achieve.  
  
This is a websocket read with a timeout, yes?

---

## Comment 4

> Username: rdzienis  
> Created at: 2022-09-12 11:39:11 UTC  
> Updated at: 2022-09-12 11:39:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1243614644  

Start reading from readMessageToByte.  
  
```  
void Connection::setInactivity(const boost::system::error_code& e)  
{  
    if (e == boost::asio::error::operation_aborted)  
    {  
        // Timer was stopped due to an incoming message - it's fine  
        return;  
    }  
    else if (e)  
    {  
        PRINT_INFO("Timer failed - ", e.what());  
    }  
    else  
    {  
        PRINT_INFO("Disconnecting client due to timeout - was ", session.timeout);  
        session.active = false;  
    }  
}  
  
  
void Connection::sendResponse(std::vector<uint8_t> response, boost::asio::yield_context yield)  
{  
    boost::beast::multi_buffer resp;  
    boost::system::error_code ec;  
  
    (*ws).text((*ws).got_text());  
    size_t n = buffer_copy(resp.prepare(response.size()), boost::asio::buffer(response));  
    resp.commit(n);  
  
    (*ws).async_write(resp.data(), yield[ec]);  
    if (ec)  
    {  
  
        return showErrorMessage(ec);  
    }  
    else  
    {  
        PRINT_INFO("Request processed - sent response");  
    }  
}  
  
  
std::vector<uint8_t> Connection::readMessageToByte(boost::asio::yield_context yield, boost::asio::io_context& ioc)  
{  
    boost::beast::flat_buffer buffer;  
    std::vector<uint8_t> data;  
    boost::system::error_code ec;  
  
    session.active = true;  
    boost::asio::deadline_timer t(ioc, boost::posix_time::seconds(session.timeout));  
    t.async_wait(boost::bind(&Connection::setInactivity, this, boost::asio::placeholders::error()));  
  
    (*ws).async_read(buffer, yield[ec]);  
  
    if (!session.active)  
    {  
        std::vector<uint8_t> messageBeforeDisconnect = {1,2,3,4}  
  
        sendResponse(messageBeforeDisconnect, yield);  
        return data; //empty  
    }  
    else if (ec == boost::beast::websocket::error::closed || ec)  
    {  
        PRINT_INFO("Connection terminated - ", ec.message());  
        return data; //empty  
    }  
  
    data.resize(buffer.data().size());  
    boost::asio::buffer_copy(boost::asio::buffer(data, data.size()), buffer.data(), buffer.data().size());  
  
    return data;  
}  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-09-12 14:18:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1243812853  

The Websocket CLOSE message has a place to put both a numeric close code and a textual close reason, why don't you use that? Beast lets you set them both:  
https://www.boost.org/doc/libs/1_80_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html

---

## Comment 6

> Username: rdzienis  
> Created at: 2022-09-13 09:02:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1245112484  

@vinniefalco How Can I call this async_close method then?  
I would like to send a full frame before disconnecting. Is it possible to put a vector<uint8_t> in this "textual close reason"?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-09-13 13:31:30 UTC  
> Updated at: 2022-09-13 13:31:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1245419045  

I think there's a limit of 128 bytes

---

## Comment 8

> Username: madmongo1  
> Created at: 2022-09-13 15:11:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1245557347  

```  
/// The type representing the reason string in a close frame.  
using reason_string = static_string<123, char>;  
  
/// The type representing the payload of ping and pong messages.  
using ping_data = static_string<125, char>;  
```  
https://github.com/boostorg/beast/blob/6ada618c120b50a3a394e4427d99d14ae78b3066/include/boost/beast/websocket/rfc6455.hpp#L158

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-09-13 15:59:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1245619005  

123, close enough :)

---

## Comment 10

> Username: rdzienis  
> Created at: 2022-09-14 10:04:57 UTC  
> Updated at: 2022-09-14 10:06:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1246533042  

I think it will work out for me, I just don't know how to call async_close..  
  
```  
template<  
    class CloseHandler = net::default_completion_token_t<[executor_type]>>  
[DEDUCED]  
async_close(  
    close_reason const& cr,  
    CloseHandler&& handler = net::default_completion_token_t< [executor_type] >{});  
```  
  
what is CloseHandler?

---

## Comment 11

> Username: rdzienis  
> Created at: 2022-09-15 07:23:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1247686945  

Is that possible to get some help with that? @vinniefalco or @madmongo1

---

## Comment 12

> Username: madmongo1  
> Created at: 2022-09-15 09:50:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1247862805  

CloseHandler is an asio-style completion handler or completion token. Just like any other asio async operation.

---

## Comment 13

> Username: rdzienis  
> Created at: 2022-09-15 10:33:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1247912272  

Could you provide me any example of its usage? I can't find anything that would help me with completing that task.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2022-09-16 15:51:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1249527531  

Did you have a look at the documentation? https://www.boost.org/doc/libs/1_80_0/libs/beast/doc/html/beast/using_websocket/control_frames.html#beast.using_websocket.control_frames.close_frames

---

## Comment 15

> Username: khernet  
> Created at: 2022-09-21 14:50:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1253823960  

I've just read in the documentation that for the async operations we have to use a handler. As you mentioned above, CloseHandler may be good, but as @rdzienis said, **we can't find any examples of its usage.**   
Should we implement it from the beginning just like any other handler or is there a way we could use an already written one?  
  
We want to use the **"ws.close(close_code::normal);"** function that @vinniefalco recommended, but it is impossible without the handler.  
Could you explain in more detail how exactly should we manage with the handlers in our situation @madmongo1?

---

## Comment 16

> Username: ashtum  
> Created at: 2024-01-03 08:57:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2519#issuecomment-1875030693  

> I've just read in the documentation that for the async operations we have to use a handler. As you mentioned above, CloseHandler may be good, but as @rdzienis said, **we can't find any examples of its usage.** Should we implement it from the beginning just like any other handler or is there a way we could use an already written one?  
>   
> We want to use the **"ws.close(close_code::normal);"** function that @vinniefalco recommended, but it is impossible without the handler. Could you explain in more detail how exactly should we manage with the handlers in our situation @madmongo1?  
  
I recommend reading about [Completion Tokens](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/overview/model/completion_tokens.html) in the Asio documentation.
