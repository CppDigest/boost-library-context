# #2296 - when does boost beast calls async_read for http reads [Closed]

> Username: luxapana  
> Created at: 2021-08-06 05:34:26 UTC  
> Updated at: 2022-09-24 05:11:16 UTC  
> Closed at: 2022-09-24 05:11:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2296  

I am using boost beast to invoke a simple RESTful service asynchronously.  
  
```  
http::async_read(_stream, _buffer, _res,  
                     beast::bind_front_handler(  
                         &Session::on_read,  
                         shared_from_this()));  
```  
  
I like to know when on_read gets called? Does it get called for each and every socket read? Or is it called only after a valid response is fully received? In this case, how does the it know that the response is complete? using content-length header of the response?  
  
How does this change when chunked encoding is used?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-08-06 05:39:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2296#issuecomment-894014852  

on_read is a completion handler. It will be called exactly once when the http read composed operation has completed, whether successfully or because of an error.  
  
Multiple async operations may happen in the background prior to it being called.  
  
on_read will be called as if by a call to post(() and will therefore execute in the context of the stream’s associated executor.

---

## Comment 2

> Username: luxapana  
> Created at: 2021-08-06 05:51:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2296#issuecomment-894018518  

Thanks!   
One more related question I like to get some help with.  
  
Our app is supposed to make repeated calls to same host:port. To make things efficient, we would like to keep the TCP connection live with the server and maintain a connection pool.   
1. Is there any support for pooling from beast?  
2. even if we set keeplive to true (and/or use http 1.1) some servers do not seem to keep the connection live. For example, I tried google.com and it seems to disconnect the transport after request is complete. Is there way in beast to identify such servers, so that we can stop re-using the connection from our end? (Something like on_disconnected callback will also help)  
3. How fast is resolve/connect? When a server does not want to keep the connection live, we will have to fallback to async_resolve/async_connect/async_write/async_read. How fast would this be? For example, does it cache resolve results or does it depend on OS DNS caches?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-08-06 05:56:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2296#issuecomment-894020270  

There is no support for automatic pooling in beast, but I did a demo of it in one of my cpp alliance blogs.  
  
Whether the server honours your keepalive request is ibtirely up to it. But in general unless you’re doing something wrong it will do so for a limited time.  
  
after the first resolve it’s pretty much instant as your OS will cache name lookups.  
  
reconnecting tcp requires 3 hops. it’s more a latency issue than a work issue.

---

## Comment 4

> Username: luxapana  
> Created at: 2021-08-06 06:11:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2296#issuecomment-894027644  

Thanks. How do we get notified asynchronous when an error such as  
disconnection occurs?  
  
On Fri, 6 Aug 2021, 11:26 Richard Hodges, ***@***.***> wrote:  
  
> There is no support for automatic pooling in beast, but I did a demo of it  
> in one of my cpp alliance blogs.  
>  
> Whether the server honours your keepalive request is ibtirely up to it.  
> But in general unless you’re doing something wrong it will do so for a  
> limited time.  
>  
> after the first resolve it’s pretty much instant as your OS will cache  
> name lookups.  
>  
> reconnecting tcp requires 3 hops. it’s more a latency issue than a work  
> issue.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2296#issuecomment-894020270>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AK4QRXTP7ZNJSYX4M3FM2OTT3N2RZANCNFSM5BVGWOLA>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&utm_campaign=notification-email>  
> .  
>

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-08-06 06:13:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2296#issuecomment-894028396  

No, it’s a feature of the way that TCP works that you will only actually know if the connection is still up when you next try to use it.  
  
I’ll dig out my blog post, sec

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-08-06 06:14:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2296#issuecomment-894028818  

https://cppalliance.org/richard/2021/01/01/RichardsNewYearUpdate.html

---

## Comment 7

> Username: luxapana  
> Created at: 2021-08-06 06:17:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2296#issuecomment-894030071  

But in a typical disconnect, the FIN can be sent be the initiator which can  
be captured in the other end. I understand that this is not possible during  
network/server failures however.  
  
On Fri, 6 Aug 2021, 11:43 Richard Hodges, ***@***.***> wrote:  
  
> No, it’s a feature of the way that TCP works that you will only actually  
> know if the connection is still up when you next try to use it.  
>  
> I’ll dig out my blog post, sec  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2296#issuecomment-894028396>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AK4QRXUSSXD4MG6FPPZMGNLT3N4QJANCNFSM5BVGWOLA>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&utm_campaign=notification-email>  
> .  
>

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-08-06 07:09:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2296#issuecomment-894053241  

Yes, if you go through the clean shutdown (and the packets actually arrive) then each peer's network layer is aware that the connection is down. However the user only gets to find out next time he tries to send or receive on the closed socket.  
  
There is of course the case where the shutdown is not clean as well.  
  
The only reliable way to know if an http connection is "up" is to:  
  
1. try to send the request  
2. on first error, reconnect  
3. if error on reconnect, stop  
3. try to send again  
4. on second error, stop  
5. wait for response  
  
Example in the code accompanying the blog

---

## Comment 9

> Username: madmongo1  
> Created at: 2021-08-06 09:25:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2296#issuecomment-894130831  

You still only know if the network card received a FIN the next time you want to use the socket.  
There’s no OS level callback to inform you.  
Even if there was, there would be a crossing case.

---

## Comment 10

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2296#issuecomment-1008220749  

This issue has been open for a while with no activity, has it been resolved?
