# #240 - asio-less network API [Closed]

> Username: vinniefalco  
> Created at: 2025-08-23 16:12:33 UTC  
> Updated at: 2025-08-26 18:16:51 UTC  
> Closed at: 2025-08-26 18:16:51 UTC  
> Url: https://github.com/boostorg/cobalt/issues/240  

Currently Cobalt allows for a compiled lib version of I/O using coroutines wrapped around an Asio implementation (https://github.com/boostorg/cobalt/tree/develop/include/boost/cobalt/io). It would be nice for all of the Asio types to be hidden in the library rather than included in the public API.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-08-25 00:11:55 UTC  
> Url: https://github.com/boostorg/cobalt/issues/240#issuecomment-3218478150  

Can you provide a rationale for users, who like me, use a full boost install and have boost/asio readily available?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2025-08-25 00:18:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/240#issuecomment-3218485045  

Are you talking about things like this:  
  
https://github.com/boostorg/cobalt/blob/develop/include/boost/cobalt/io/socket.hpp#L34  
  
or `asio::ssl::stream_base`  
  
https://github.com/boostorg/cobalt/blob/develop/include/boost/cobalt/io/ssl.hpp#L44  
  
?
