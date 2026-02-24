# #1769 - Force async_read to read a byte at a time [Closed]

> Username: rogueresistor  
> Created at: 2019-11-18 21:22:05 UTC  
> Updated at: 2019-11-18 21:35:25 UTC  
> Closed at: 2019-11-18 21:35:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1769  

### Version of Beast  
  
Version included with Boost 1.71.0  
  
### Problem Statement  
  
I want to use the `request`/`response` objects that Beast includes, however for the `response` being read from the remote socket I absolutely need it to not read any data past the last `\r\n` from the socket.  
  
I want to write a way to transparently set up a `HTTP CONNECT` based proxy session, and after the last `\r\n` anything else is connected directly to the remote endpoint. I want to at this point hand the socket off to some other code that is not aware of any buffers, nor should it be because it just needs to care about having a socket to read/write from.  
  
So is there some way to have `async_read` stop reading as soon as it sees the `\r\n`?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-18 21:30:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1769#issuecomment-555217958  

> So is there some way to have async_read stop reading as soon as it sees the \r\n?  
  
Nope. If you want to do this, read from the socket yourself until you see `"\r\n\r\n"`, and use `beast::http::parser` to manually submit the buffer for parsing.

---

## Comment 2

> Username: rogueresistor  
> Created at: 2019-11-18 21:35:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1769#issuecomment-555219893  

Alright, thanks @vinniefalco!
