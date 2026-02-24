# #2717 - Question: Does websocket::stream automatically perform close handshakes upon errors? [Closed]

> Username: ecorm  
> Created at: 2023-08-11 01:11:47 UTC  
> Updated at: 2023-08-12 06:41:03 UTC  
> Closed at: 2023-08-12 06:41:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2717  

Does `websocket::stream` automatically perform close handshakes upon errors? For example, if a remote peer sends a message that exceeds `websocket::stream::read_message_max`, will the `websocket::stream` automatically send a close handshake with an appropriate reason code?

---

## Comment 1

> Username: ecorm  
> Created at: 2023-08-11 01:56:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2717#issuecomment-1674129152  

I'm also wondering if `websocket::stream` will automatically perform a close handshake if there was a failure in the initiating handshake.

---

## Comment 2

> Username: ecorm  
> Created at: 2023-08-11 02:07:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2717#issuecomment-1674134713  

Answering my own second question, the [Handshaking](https://www.boost.org/doc/libs/1_82_0/libs/beast/doc/html/beast/using_websocket/handshaking.html) documentation page says:  
  
> If the handshake does not meet the requirements, or falls outside the range of allowed parameters specified by stream options set previously by the caller, the stream sends back an HTTP response with a status code indicating an error.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-08-11 02:47:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2717#issuecomment-1674152437  

No it won't. beast is pretty low level, so you'll need to handle all that manually

---

## Comment 4

> Username: ecorm  
> Created at: 2023-08-11 02:59:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2717#issuecomment-1674157144  

> No it won't. beast is pretty low level, so you'll need to handle all that manually  
  
Good. I prefer to do it manually than guess what Beast is doing automagically and possibly fight against it.  
  
What I'm doing for now is checking the error codes from `websocket::stream::async_read` and `websocket::stream::async_write` and performing the following mapping to close codes:  
  
```c++  
boost::beast::websocket::close_code  
websocketErrorToCloseCode(boost::beast::error_code ec)  
{  
    using boost::beast::websocket::close_code;  
    using boost::beast::websocket::condition;  
    using boost::beast::websocket::error;  
  
    if (ec == condition::protocol_violation)  
        return close_code::protocol_error;  
    if (ec == error::buffer_overflow || ec == error::message_too_big)  
        return close_code::too_big;  
    return close_code::internal_error;  
}  
```
