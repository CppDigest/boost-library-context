# #2698 - questions about handling more than one (nested) websocket connection [Closed]

> Username: eemincetin  
> Created at: 2023-06-23 14:29:46 UTC  
> Updated at: 2023-10-28 16:32:30 UTC  
> Closed at: 2023-10-28 16:32:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2698  

I've read your async examples, I've created a websocket class that has multiple handlers. Basically I'm listening to coin websockets of a variety of crypto exchange servers. That is okay up to here. But now, I need to connect a websocket and listen for one response. after that response came to me, I'll use that response to connect another websocket, while first websocket is still running (I need to send ping periodically to it, but nothing more than this) and in this new websocket I'll have new handlers and to do somethings.  
  
While connecting coin websockets, I can use an io_context object and my code is stuck in ioc.run(). But now, how to handle a scenario I've just explained. How to build a connection or how to handle more than one connection? I couldn't relate examples with my scenario.  
  
And I'm using async_ssl example to build my all system. I don't really know conceptually what coroutines are. maybe they can help? overall I need to know what should I know/apply to proceed.   
  
thanks Vinnie for this amazing library

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-06-26 00:57:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2698#issuecomment-1606376922  

`ioc.run()` enters an event loop and will remain there until all the work is finished, which means all connections are closed in your case.  
  
I suspect that you'll want to `asio::post` the second job to the io_context once the connection is accepted. You could also look into coroutines if you want an easier to reason about model.

---

## Comment 2

> Username: eemincetin  
> Created at: 2023-07-03 12:44:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2698#issuecomment-1618191847  

thanks for response @klemens-morgenstern , do you have any comment on this @vinniefalco ?

---

## Comment 3

> Username: ashtum  
> Created at: 2023-08-19 05:17:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2698#issuecomment-1684824258  

@eemincetin Is this still open?
