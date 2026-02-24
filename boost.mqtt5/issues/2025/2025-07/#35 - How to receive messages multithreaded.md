# #35 - How to receive messages multithreaded? [Closed]

> Username: autoantwort  
> Created at: 2025-07-27 15:23:57 UTC  
> Updated at: 2025-11-06 09:35:27 UTC  
> Closed at: 2025-11-06 09:35:27 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/35  

In the only example how to receive messages this is done like this:  
https://github.com/boostorg/mqtt5/blob/5375062894f192351d2827e33412179471e01113/example/receiver.cpp#L91-L93  
Which means only a single thread is used to process incoming messages. It is possible to use multiple threads to process subscribed topics?

---

## Comment 1

> Username: autoantwort  
> Created at: 2025-07-27 20:12:04 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/35#issuecomment-3124700713  

Ok probably it is better to only have one thread getting the messages and then multiple worker threads handling the hard work (writing stuff into a database (not async))

---

## Comment 2

> Username: autoantwort  
> Created at: 2025-07-27 22:13:25 UTC  
> Updated at: 2025-07-27 22:13:33 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/35#issuecomment-3124756508  

Interestingly the example crashes with a `read access violation` on windows if you include `<spdlog/spdlog.h>` before `<boost/asio/use_awaitable.hpp>`

---

## Comment 3

> Username: biljazovic  
> Created at: 2025-07-28 09:28:08 UTC  
> Updated at: 2025-07-28 09:34:20 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/35#issuecomment-3126354592  

`mqtt_client` is not thread-safe.  
  
> Ok probably it is better to only have one thread getting the messages and then multiple worker threads handling the hard work (writing stuff into a database (not async))  
  
This sounds like reasonable approach.  
  
> Interestingly the example crashes with a read access violation on windows if you include <spdlog/spdlog.h> before <boost/asio/use_awaitable.hpp>  
  
I couldn't reproduce this with MSVC 19.44.35213, C++20 and spdlog v1.x. It would help a lot if you could share a full example with the versions of your compiler and dependencies. Thanks!  
  
One thing to watch out for is that you need to construct the client with either an `io_context` running on a single thread, or a `boost::asio::strand`, **and** call all member functions of the client within that context/strand.
