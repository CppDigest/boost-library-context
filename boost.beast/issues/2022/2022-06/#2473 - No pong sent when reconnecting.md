# #2473 - No pong sent when reconnecting [Closed]

> Username: alayaoq  
> Created at: 2022-06-23 11:10:34 UTC  
> Updated at: 2022-08-25 12:25:47 UTC  
> Closed at: 2022-08-25 12:25:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2473  

### Version of Beast  
1.66  
  
### Steps necessary to reproduce the problem  
I'm trying to implement a WebSocket client using boost::beast. The first connection and data exchanges are fine.  
And when I close the server and relaunch it again, the connection is properly established again.  
However, it is only for a few minutes as the client doesn't reply to Ping requests anymore.  
  
In my Connect function (that I call to connect the first time and reconnect when a disconnection is detected), I properly reset the io_context and the websocket::stream:  
```  
FContext.restart();  
FWebsocket.emplace(FContext);  
...  
net::connect(FWebsocket->next_layer(), endpoints);  
```  
  
What am I missing?  
  
Note: I've also added the Ping mechanism shown in the early advanced-server example as I need it in my client.  
  
### All relevant compiler information  
Compiling with cygwin (g++ (GCC) 10.2.0)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-26 14:47:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2473#issuecomment-1166554293  

Are you using synchronous or asynchronous I/O functions?

---

## Comment 2

> Username: alayaoq  
> Created at: 2022-06-27 07:00:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2473#issuecomment-1166956380  

Hello Vinnie,  
  
Thank you for your reply.  
  
I'm using the sync connect/handshake. The others I/O functions (read, write, close, ping) are async.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-06-27 16:31:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2473#issuecomment-1167579225  

You cannot mix sync and async

---

## Comment 4

> Username: alayaoq  
> Created at: 2022-06-30 09:08:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2473#issuecomment-1170964854  

Hello,   
  
Thank you for the tip. I thought we could as long as we don't mix them in //.  
So I tried to replace my sync connect/handshake with their async version, but I still have the issue.  
  
I'll try to write a minimal code reproducing the issue asap. But if you have any other idea in the meantime let me know  
  
Thank you for the support

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-06-30 14:45:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2473#issuecomment-1171311657  

You also cannot reuse a websocket, you have to destroy it and construct a new one.

---

## Comment 6

> Username: alayaoq  
> Created at: 2022-07-13 08:16:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2473#issuecomment-1182912679  

Yep I take care of this as well  
(the minimal code is coming, sorry for the delay)

---

## Comment 7

> Username: alayaoq  
> Created at: 2022-08-25 12:25:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2473#issuecomment-1227188919  

Ok so I built a minimal code and it turns out... the problem is not present  
In fact, I was basically blocking the thread handling the IO functions due to some silly line of codes...  
  
Anyway, Thanks Vinnie for the reactivity! Much appreciated!
