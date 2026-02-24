# #2639 - Guidance on the number of threads [Closed]

> Username: asimeqi  
> Created at: 2023-02-16 15:54:07 UTC  
> Updated at: 2023-02-16 16:21:29 UTC  
> Closed at: 2023-02-16 16:21:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2639  

I am writing some server code based on the websocket async example: https://github.com/boostorg/beast/blob/develop/example/websocket/server/async/websocket_server_async.cpp  
It's not clear to me how many context threads I need to have. My server will have up to 8 clients that will create websockets to hopefully have a permanent connection with the server. Do I need 8 (or more) context threads or 1 will be enough since we are using async?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-02-16 15:58:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2639#issuecomment-1433316356  

Start with 1 and see how it goes

---

## Comment 2

> Username: asimeqi  
> Created at: 2023-02-16 16:08:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2639#issuecomment-1433331302  

With one thread and one client (Chrome) I occasionally get this error:  
`on_read failure: stream truncated`. This seems to be okay (or not?).  
But if I have multiple clients I also some times see this error:  
`on_read failure: The I/O operation has been aborted because of either a thread exit or an application request`  
  
As far as I can tell neither of these errors cause any messages between server and client to be lost, but it seems that the second error (and maybe the first?) might cause some trouble in production.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-02-16 16:15:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2639#issuecomment-1433342152  

Yeah Google thinks that closing the socket non-gracefully (in other words without sending a TCP FIN packet) is faster and produces less latency. "stream truncated" means that the client closed the socket without sending a FIN.  
  
The other error, `asio::error::operation_aborted` means that the I/O was canceled because the socket was closed. This is also normal. You should probably not log operation_aborted since it is a normal consequence of closing a socket.  
  
As for the "stream truncated" this is also a normal occurrence.

---

## Comment 4

> Username: asimeqi  
> Created at: 2023-02-16 16:21:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2639#issuecomment-1433351838  

Thanks, that sounds good.
