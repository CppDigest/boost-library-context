# #3067 - Using async_read_some from SSL with async_close from websocket [Open]

> Username: andrzelipuk  
> Created at: 2026-01-14 13:47:37 UTC  
> Updated at: 2026-01-25 14:07:58 UTC  
> Url: https://github.com/boostorg/beast/issues/3067  

Hello!  
  
I have a question related to the usage of the `boost::beast::websocket::stream` and `boost::beast::ssl::stream`:  
Can `async_read_some()` from SSL layer be used together with `async_close()` from the wss stream?  
I get an assertion error at `boost/beast/core/detail/stream_base.hpp:116`  while closing websocket using `async_close()`:  
`BOOST_ASSERT(! *b_);`

---

## Comment 1

> Username: ashtum  
> Created at: 2026-01-25 14:07:58 UTC  
> Url: https://github.com/boostorg/beast/issues/3067#issuecomment-3796719024  

Hi,  
  
No read or write operations on the underlying stream are permitted after a WebSocket stream is established. This is because the WebSocket stream needs to perform read and write operations on the underlying stream to complete certain actions. For example, `websocket::stream::async_close` must write a close frame and then read until a close frame is received. Therefore, it will conflict with your `async_read_some` call on the underlying stream.
