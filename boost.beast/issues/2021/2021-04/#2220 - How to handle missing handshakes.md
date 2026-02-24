# #2220 - How to handle missing handshakes? [Closed]

> Username: Moerten  
> Created at: 2021-04-22 08:22:28 UTC  
> Updated at: 2022-01-09 05:17:26 UTC  
> Closed at: 2022-01-09 05:17:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2220  

Hi,  
  
I implemented a async client SSL websocket based on this example:  
  
```  
https://www.boost.org/doc/libs/1_66_0/libs/beast/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp  
```  
  
If I connect e.g. to "wss://ws.bitstamp.net:443" everything works fine and the callstack is like:  
  
```  
async_resolve  
OnResolve  
async_connect  
OnConnect  
async_handshake (SSL)  
OnHandshakeSSL  
async_handshake  
OnHandshake  
async_read_some  
...  
```  
  
In case there is an error while the connection is established the socket gets closed and async_resolve gets called again to trigger a reconnect. So far so good.  
  
But if I instead connect to e.g. "wss://echo.websocket.org:80" port 80 intead of 443 to produce an error on purpose the callstack is like:  
  
```  
async_resolve  
OnResolve  
async_connect  
OnConnect  
async_handshake (SSL)  
OnHandshakeSSL  
stream truncated (error 1)  <-- Fine that was expected to happen  
close (and async_resolve retriggered to try to reconnect)  
async_resolve  
OnResolve  
async_connect  
OnConnect  
async_handshake (SSL)  
```  
And at this point nothing happens any more and  
the callback mechanism gets broken because  
the OnHandshakeSSL callback becomes not called again.  
It seems there is no handshake being send from the server again.  
  
I am wondering now how to handle this case correctly.  
Shouldn't be there a timeout error getting called after a while from beast?  
  
And as a side note. I am getting a name conflict for "Int" from  
  
```  
boost\boost\beast\http\impl\field.ipp(83,1): warning C4459: declaration of 'Int' hides global declaration  
```  
  
Maybe it's possible to change this. Vinnie Falce also commented this line for using size_t instead.  
Luckily I can silence this conflict by disabling the warning.  
  
But there is another terrible naming conflict for "UI"  
because #include <boost/asio/ssl/stream.hpp> or #include <boost/beast/websocket/ssl.hpp> makes these horrible OpenSSL typdefs wasting the global namespace. Maybe it's possible to hide this oldschool OpenSSL stuff by an extra define or so. How can someone come to the idea to make a typedef "UI" in the global namespace aaargghhh. Because of this the only solution was to disable precompiled headers for files inluding this ssl stuff.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-04-22 08:41:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2220#issuecomment-824656987  

Are you destroying and recreating the websocket and its underlying SSL stream?

---

## Comment 2

> Username: Moerten  
> Created at: 2021-04-22 08:48:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2220#issuecomment-824661247  

No, because that feels like wasting resources. I only call "close" and/or "shutdown" (depending on the socket). But if this is the correct way maybe I should do so?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-04-23 11:20:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2220#issuecomment-825589976  

Yes you need to.   
For two reasons:  
1. Once the websocket has been connected, its state is not reset on disconnect (usually this is not necessary as most implementations will just throw away the websocket after disconnection)  
2. We have had difficulty with ssl::stream and reconnections. I have not had an answer back from the author as to whether it is intended to be reused or not.  
  
So generally if you want reconnection capability, either:  
1. store the websocket::stream in an optional or variant so it can be destroyed/rebuilt esily  
2. embed the websocket::stream in a connection object which is owned by a shared_ptr (this is probably the most common use case)

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 16:10:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2220#issuecomment-850857836  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2022-01-09 05:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2220#issuecomment-1008232420  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
