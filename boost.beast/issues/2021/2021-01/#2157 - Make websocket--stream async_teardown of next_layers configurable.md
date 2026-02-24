# #2157 - Make websocket::stream async_teardown of next_layers configurable [Closed]

> Username: TheStormN  
> Created at: 2021-01-27 23:51:29 UTC  
> Updated at: 2021-01-28 00:37:31 UTC  
> Closed at: 2021-01-28 00:37:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2157  

Currently `async_close()` will tear down next layers(like ssl::stream and tcp::socket) which is pretty nice. However, there are cases where the next layers are managed additionally for some custom purposes(like keep-alive) and then shutting down the whole chain is basically making such tasks hard to achieve.  
  
My proposal is to add an option to `set_option()` about that behavior. Of course, by default keep it `on` so there will be no behavioral change, but at least whoever want to manage the layers by himself will have the possibility to do so.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-01-28 00:00:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2157#issuecomment-768658270  

This behaviour of async_close was added recently as a result of a user  
request.  
  
Do you have a specific use case for leaving a socket open after the  
websocket is finished with, or is this a theoretical concern?  
  
  
On Thu, 28 Jan 2021 at 00:51, Nikolay Baklicharov <notifications@github.com>  
wrote:  
  
> Currently async_close() will tear down next layers(like ssl::stream and  
> tcp::socket) which is pretty nice. However, there are cases where the next  
> layers are managed additionally for some custom purposes(like keep-alive)  
> and then shutting down the whole chain is basically making such tasks hard  
> to achieve.  
>  
> My proposal is to add an option to set_option() about that behavior. Of  
> course, by default keep it on so there will be no behavioral change, but  
> at least whoever want to manage the layers by himself will have the  
> possibility to do so.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2157>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSMHP7M5R74MT4QNINDS4CRJFANCNFSM4WWED6WA>  
> .  
>  
--   
Richard Hodges  
hodges.r@gmail.com  
office: +442032898513  
home: +376841522  
mobile: +376380212

---

## Comment 2

> Username: TheStormN  
> Created at: 2021-01-28 00:16:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2157#issuecomment-768664240  

I'm that very same user. :) The thing before was that the job was halfway done. If the next layer is ssl::stream it would be shutdown, but leaving the tcp socket, on the other hand if the next layer is tcp socket, then it will shut it down.  My previous request was to shutdown the whole chain to make the operation consistent.  
  
However, I have a use case where the next layer is ssl::stream(with tcp socket) and I just want to close the websocket, without closing the SSL stream and the tcp socket and yes my experiments are purely for fun and research, there is no a real showstopper currently on real projects, but yeah, I thought that a simple `option` for that behavior would be useful. :)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-01-28 00:28:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2157#issuecomment-768668969  

Definitely not, because the WebSocket specification says that when the websocket connection is closed, the TLS connection MUST be torn down. You are asking for behavior from Beast that goes against the RFC. If you really need to do that, write your own NextLayer type which is a wrapper around socket or TLS stream, and implement teardown for that.

---

## Comment 4

> Username: TheStormN  
> Created at: 2021-01-28 00:37:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2157#issuecomment-768672453  

Ook, take it easy man, I haven't read the RFC in such detail. Yes, I know about the workaround with custom layer, I was just wondering if I can avoid it. Anyway, thanks for the info.
