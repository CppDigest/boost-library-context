# #3038 - send many subscription messages with websocket client [Open]

> Username: eaziz4  
> Created at: 2025-10-03 21:11:47 UTC  
> Updated at: 2025-10-07 23:01:05 UTC  
> Url: https://github.com/boostorg/beast/issues/3038  

I'm looking to build an async/coro websocket client that will stay open indefinitely and continually subscribe/unsubscribe for more data based on the responses I get from my initial subscriptions.  The attached gist is mainly a clone of the async websocket client example but needs modification to accomplish this.  My desired flow is around the following where I connect to the server, subscribe for data(ws client store sthat in a map/deque/queue to be processed in a separate thread(processing will lead to subscribing for more data)), and have a separate thread that polls the received messages every second(if there are none, I'll do nothing and keep polling.  I never want to close the connection).  This might not be the best way to describe this, but the existing examples aren't setup for multiple subscriptions/offloading processing the received messages and any help would be appreciated.  Thanks!  
  
```  
// connect to server  
net::io_context ioc;  
ssl::context ctx{ssl::context::tlsv13_client};  
ctx.set_default_verify_paths();  
auto session = std::make_shared<Session>(ioc, ctx);  
session->connect(path.c_str(), "443", target.c_str(), "", headers);  
  
//initial subscriptions(ws client stores received messages in a datastructure to offload work)  
session->subscribe(request);  
session->subscribe(request1);  
session->subscribe(request2);  
session->subscribe(request3);  
ioc.run();  
  
// separate thread reads   
for (const auto& message : session->messages_) {  
    // process message and remove from messages_  
    // process message may send more subscriptions  
    if (shouldSubscribe) {  
        session->subscribe(newRequest);  
    }  
}  
  
  
```  
  
https://gist.github.com/eaziz4/fb8cd541a3986f606161e2ab10854ec8

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-10-03 22:06:25 UTC  
> Url: https://github.com/boostorg/beast/issues/3038#issuecomment-3367353111  

why do you want to "offload" the processing of messages to a separate thread? Are they computationally expensive?

---

## Comment 2

> Username: eaziz4  
> Created at: 2025-10-04 01:30:57 UTC  
> Url: https://github.com/boostorg/beast/issues/3038#issuecomment-3367739149  

I'm fine not offloading them as they aren't extremely expensive, I just figured by not doing so it would slow things down.  I also thought of that as a way to generalize my websocket client as I want to reuse this client for a few different use cases and not have to make a separate client for separate processing logic

---

## Comment 3

> Username: vinniefalco  
> Created at: 2025-10-05 22:37:34 UTC  
> Url: https://github.com/boostorg/beast/issues/3038#issuecomment-3369388327  

I would start with doing everything on a single thread. Get it working, and then measure. If you are happy with it, you are done :) If not, then identify why you are not happy with it and then consider what should change. You can ask other folks if you get to this point (and also post your measurements).

---

## Comment 4

> Username: eaziz4  
> Created at: 2025-10-06 23:07:26 UTC  
> Updated at: 2025-10-06 23:30:33 UTC  
> Url: https://github.com/boostorg/beast/issues/3038#issuecomment-3374589109  

I think I worded my original question really poorly.  My use case is maintaining a real time orderbook.  I _can_  put all of my processing logic into my client and that should be fine where I have a datastructure of processed entities as a member of my client(just not reusable when I need to maintain a real time orderbook for a completely separate product and I have different processing logic).  Where I'm struggling right now is that I need to poll every 0.5 seconds and save these entities into my backend.  I need some sort of locking for this and figured that by making an issue I might get more professional answers than what I would try to do or an example from someone who has already accomplished this.

---

## Comment 5

> Username: ashtum  
> Created at: 2025-10-07 07:06:44 UTC  
> Url: https://github.com/boostorg/beast/issues/3038#issuecomment-3375491758  

Have you considered using an [experimental::basic_concurrent_channel](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/experimental__basic_concurrent_channel.html) to send new subscribe requests to the other thread?

---

## Comment 6

> Username: eaziz4  
> Created at: 2025-10-07 20:34:16 UTC  
> Url: https://github.com/boostorg/beast/issues/3038#issuecomment-3378619766  

> Have you considered using an [experimental::basic_concurrent_channel](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/experimental__basic_concurrent_channel.html) to send new subscribe requests to the other thread?  
  
Are there any examples of a websocket client that uses this?  This logically sounds like what I want(to send a new subscription request while processing), but I'm confused on how to implement this

---

## Comment 7

> Username: sehe  
> Created at: 2025-10-07 21:23:04 UTC  
> Updated at: 2025-10-07 21:23:37 UTC  
> Url: https://github.com/boostorg/beast/issues/3038#issuecomment-3378768353  

> Are there any examples of a websocket client that uses this? This logically sounds like what I want(to send a new subscription request while processing), but I'm confused on how to implement this  
  
Feel free to nose through my answers on StackOverflow. Here's a quick find that combines channels with websockets: https://stackoverflow.com/questions/78120525/c20-coroutines-read-write-websocket/78124207#78124207. There's many more https://stackoverflow.com/search?q=user%3A85371+%5Bboost-asio%5D+%5Bwebsocket%5D though not everybody accurately tags stuff (e.g. I've posted live samples for binance, bybit and others), so google with site:stackoverflow.com sometimes helps

---

## Comment 8

> Username: vinniefalco  
> Created at: 2025-10-07 23:01:05 UTC  
> Url: https://github.com/boostorg/beast/issues/3038#issuecomment-3378982592  

You can set the timer and have the timer's completion handler run on the same strand as the websocket. This way, you are guaranteed that when your timer is being processed that the websocket is not being accessed at the same time.
