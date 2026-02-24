# #135 - Replace the request queue in the connection with a channel [Closed]

> Username: mzimbres  
> Created at: 2023-08-06 11:34:01 UTC  
> Updated at: 2023-08-31 10:57:51 UTC  
> Closed at: 2023-08-31 10:57:51 UTC  
> Url: https://github.com/boostorg/redis/issues/135  

Asio channels are multiple-producers and multiple-consumer channels and can be used to simplify the connection internals by replacing the `std::queue` and per-request allocation of a timer. See https://github.com/chriskohlhoff/asio/blob/master/asio/src/examples/cpp20/channels/mutual_exclusion_1.cpp

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-08-31 10:57:51 UTC  
> Url: https://github.com/boostorg/redis/issues/135#issuecomment-1700822973  

After inspecting a bit the implementation, it turns out that a channel can't be used to replace the internal queue because we need the ability to partition the queue and set priorities on requests to support reconnection with proper resp3 handshake.
