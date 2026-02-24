# #2714 - Question: Unclear websocket::stream::async_read error code when other peer closes with "problem" reason [Closed]

> Username: ecorm  
> Created at: 2023-08-10 01:57:32 UTC  
> Updated at: 2023-08-10 04:07:09 UTC  
> Closed at: 2023-08-10 04:07:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2714  

The project README says to post questions here in the issue tracker, so here it is. :grin:   
  
When a `websocket::stream::async_read` operation is aborted due to the other peer closing, what are the error codes I can expect in the `async_read` handler? Will the error code depend on the close reason given by the other peer?  
  
I'd also like to know the expected error code when the TCP connection is closed suddenly by the other peer without a proper Websocket close handshake. Would it be the same as what the underlying Boost.Asio TCP socket would emit?

---

## Comment 1

> Username: ecorm  
> Created at: 2023-08-10 02:05:05 UTC  
> Updated at: 2023-08-10 02:08:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2714#issuecomment-1672430633  

Oops, just after posting my question, I saw that I missed this part in the `async_read` documentation:  
  
> If a close frame is received, the WebSocket close procedure is performed. In this case, when the function returns, the error [error::closed](https://www.boost.org/doc/libs/1_82_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__error.html) will be indicated.  
  
What's not clear is if it still emits `error::closed` if the other peer closed with a reason indicating a problem (e.g. `websocket::too_big`).  
  
The description for `error::closed` says:  
> The WebSocket stream was **gracefully** closed at both endpoints.  
  
The word "gracefully" here is a bit confusing. If the remote peer closed with a reason indicating a problem (e.g. `websocket::too_big`), is this still considered a graceful close?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-08-10 03:54:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2714#issuecomment-1672513705  

Yes that is graceful. Graceful means the websocket was shut down properly. Not graceful would be the opposite side just closing the tcp socket i.e. dropping the connection.

---

## Comment 3

> Username: ecorm  
> Created at: 2023-08-10 04:07:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2714#issuecomment-1672522590  

Thanks @klemens-morgenstern , that answers my question.
