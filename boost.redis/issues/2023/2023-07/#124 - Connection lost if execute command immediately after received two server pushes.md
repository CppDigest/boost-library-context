# #124 - Connection lost if execute command immediately after received two server pushes [Closed]

> Username: ysc3839  
> Created at: 2023-07-26 03:39:12 UTC  
> Updated at: 2023-08-14 22:45:25 UTC  
> Closed at: 2023-07-26 07:53:48 UTC  
> Url: https://github.com/boostorg/redis/issues/124  

Add following code to cpp20_subscriber.cpp:  
```patch  
@@ -50,10 +50,13 @@ auto  
 receiver(std::shared_ptr<connection> conn) -> net::awaitable<void>  
 {  
    request req;  
    req.push("SUBSCRIBE", "channel");  
   
+   request req2;  
+   req2.push("INCR", "count");  
+  
    // Loop while reconnection is enabled  
    while (conn->will_reconnect()) {  
   
       // Reconnect to channels.  
       co_await conn->async_exec(req, ignore, net::deferred);  
@@ -68,10 +71,12 @@ receiver(std::shared_ptr<connection> conn) -> net::awaitable<void>  
             << resp.value().at(1).value  
             << " " << resp.value().at(2).value  
             << " " << resp.value().at(3).value  
             << std::endl;  
          resp.value().clear();  
+  
+         co_await conn->async_exec(req2, ignore, net::deferred);  
       }  
    }  
 }  
   
 auto co_main(config cfg) -> net::awaitable<void>  
```  
When two server pushes arrived at the same time (by execute `redis-cli PUBLISH channel msg & redis-cli PUBLISH channel msg` in shell), the connection lost.  
Is it a bug of this library? Or is it not allowed by redis protocol? What's the proper way to update something when receive server push?

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-07-26 05:49:51 UTC  
> Url: https://github.com/boostorg/redis/issues/124#issuecomment-1651020473  

This is so by design. If not consumed, server pushes will block the connection. It looks like in your case you are receiving a server push while waiting for the response to an `async_exec`, so they block each other. It is not recommended to call `async_exec` from the same coroutine that is receiving pushes for this reason. If you really need to do that, use a second connection or a queue.

---

## Comment 2

> Username: ysc3839  
> Created at: 2023-07-26 07:53:48 UTC  
> Url: https://github.com/boostorg/redis/issues/124#issuecomment-1651170240  

Thanks! I will use a second connection to receive push messages.
