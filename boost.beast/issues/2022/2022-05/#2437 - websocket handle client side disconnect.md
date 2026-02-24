# #2437 - websocket handle client side disconnect [Closed]

> Username: Tectu  
> Created at: 2022-05-25 11:53:50 UTC  
> Updated at: 2022-07-21 04:54:19 UTC  
> Closed at: 2022-06-14 17:33:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2437  

Over at [malloy](https://github.com/tectu/malloy) we're experiencing a "small issue" regarding proper handling of EOF on websocket connections (server side).  
  
The `do_disconnect()` implementation of our websocket connection looks like this:  
  
https://github.com/Tectu/malloy/blob/abdfb0938ca4b5907e3b512e9ef84dddec4cd9d3/lib/malloy/core/websocket/connection.hpp?#L319  
  
What we're observing is that the error code `ec` is holding an error when the client closes the connection. When that happens, the log of the server shows:  
```  
[error] could not close websocket: 'End of file'  
```  
When that happens, the reason (`boost::beast::websocket::close_reason`) is `1000` which, as per my understanding of `RFC6455`, indicates a normal closure.  
The value of `ec` is `2` which, according to `boost::beast::websocket::error` is field `unused1` (comment of that fields states `"failed"`).  
  
Unless I'm missing something, I think it's totally fine for the client to close the connection. We certainly don't want to spam the server log with these messages.  
  
My questions:  
- Is our implementation of the websocket connection incorrect or should we handle the evaluation of the error code differently?  
- Is there a particular (eg. different) way that the server should handle a websocket connection closure by the client? i.e. should the server side connection not invoke `async_close()` when the client closed the connection already?  
- How does one properly detect connection closures initiated by the client?

---

## Comment 1

> Username: sehe  
> Created at: 2022-05-26 21:45:41 UTC  
> Updated at: 2022-05-26 21:48:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2437#issuecomment-1139089144  

Addressing just that last bullet: "How does one properly detect connection closures initiated by the client?"  
  
Reads will return the error `error::closed` (see e.g. https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read.html)  
  
I suspect one can also register a callback for the control frames: https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/control_callback/overload1.html. In most cases the default implementation of pings/pongs/close frames will suffice.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-14 17:33:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2437#issuecomment-1155497896  

Has this been resolved?

---

## Comment 3

> Username: Tectu  
> Created at: 2022-06-14 22:03:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2437#issuecomment-1155754476  

Not yet - but I won't be able to dive back into this until July.  
Sorry for not reporting back!

---

## Comment 4

> Username: Tectu  
> Created at: 2022-07-21 02:02:18 UTC  
> Updated at: 2022-07-21 02:02:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2437#issuecomment-1190952046  

This issued turned out to be not related to neither boost nor malloy. The 3rd-party websocket client application talking to the malloy server violated the WebSocket protocol.  
  
I was thrown off by the error code of `2` seemingly corresponding to `boost::beast::websocket::error::unused1`. However, upon closer inspection:  
```cpp  
/*  The error codes error::failed and error::handshake_failed  
    are no longer in use. Please change your code to compare values  
    of type error_code against condition::handshake_failed  
    and condition::protocol_violation instead.  
              
    Apologies for the inconvenience.  
  
    - VFALCO  
*/  
#if ! BOOST_BEAST_DOXYGEN  
    unused1 = 2, // failed  
    unused2 = 3, // handshake_failed  
#endif  
```  
  
@vinniefalco Inconvenience where caused 😜

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-07-21 04:54:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2437#issuecomment-1191037265  

Interesting :)
