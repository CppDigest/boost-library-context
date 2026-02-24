# #246 - unix sockets support? [Closed]

> Username: eaziz4  
> Created at: 2025-05-02 20:41:23 UTC  
> Updated at: 2025-06-20 11:23:40 UTC  
> Closed at: 2025-06-20 11:23:40 UTC  
> Url: https://github.com/boostorg/redis/issues/246  

I don't see how to connect to my redis instance via unix socket.  I only see the host + port option in the config class.  Is this supported?

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-05-03 15:00:13 UTC  
> Url: https://github.com/boostorg/redis/issues/246#issuecomment-2848662747  

Hi @eaziz4, this is not yet supported. I would like to implement this feature but don't have much time available at the moment, so I can't make commitments about when I will do it.

---

## Comment 2

> Username: eaziz4  
> Created at: 2025-05-04 20:26:00 UTC  
> Url: https://github.com/boostorg/redis/issues/246#issuecomment-2849402989  

No worries.  Can this issue stay open until it is implemented as I would very much prefer to use this library over others, but need socket support as performance is very important for me.

---

## Comment 3

> Username: anarthal  
> Created at: 2025-05-05 09:06:03 UTC  
> Url: https://github.com/boostorg/redis/issues/246#issuecomment-2850361940  

@mzimbres both `connection` and `basic_connection<Executor>` have a `next_layer()` member that returns the underlying `asio::ssl::stream<asio::ip::tcp::socket>`. To support UNIX sockets, we need to either remove this member (backwards incompatible), change it to `asio::ssl::stream<asio::generic::socket>` (backwards incompatible) or make a new `basic_connection_v2` and `connection_v2` (maintenance problems). What are your views on this?

---

## Comment 4

> Username: mzimbres  
> Created at: 2025-05-05 20:18:15 UTC  
> Url: https://github.com/boostorg/redis/issues/246#issuecomment-2852233779  

@eaziz4 Sure, we will keep this open until we manage to implement it.  
  
@anarthal I prefer to remove `next_layer()` as I don't see how this can be any useful to users.

---

## Comment 5

> Username: mzimbres  
> Created at: 2025-06-01 21:09:30 UTC  
> Url: https://github.com/boostorg/redis/issues/246#issuecomment-2927909878  

>  but need socket support as performance is very important for me.  
  
@anarthal Given OPs need for low latency, we might also want to consider two other easy spots to reduce overall latency.  
  
1. We can avoid copying payloads [here](https://github.com/boostorg/redis/blob/0c8c6fcc095ff103e9adda708c1efb06fabfe08d/include/boost/redis/impl/multiplexer.ipp#L158) when the connection has only one ongoing `async_exec`. The multiplexer `get_write_buffer()` function could then return a `string_view` pointing directly to the `request` internal buffer instead of a copy of it, which would then be used [here](https://github.com/boostorg/redis/blob/0c8c6fcc095ff103e9adda708c1efb06fabfe08d/include/boost/redis/connection.hpp#L199).  
  
2. We can provide a `generic_flat_response` that stores all the data in a single `std::string`, so instead of [this](https://github.com/boostorg/redis/blob/0c8c6fcc095ff103e9adda708c1efb06fabfe08d/include/boost/redis/response.hpp#L35)  
  
```cpp  
using generic_response = adapter::result<std::vector<resp3::basic_node<std::string>>;  
```  
we would have  
  
```cpp  
using generic_flat_response = adapter::result<std::vector<resp3::basic_node<std::string_view>>;  
```  
eliminating the need for dynamic memory allocations altogether. Since OP seems to be operating low millisecond requests these two points could play a significant role.

---

## Comment 6

> Username: anarthal  
> Created at: 2025-06-01 22:13:30 UTC  
> Url: https://github.com/boostorg/redis/issues/246#issuecomment-2927998758  

Actually we could try doing vectored I/O if the requests are reasonably big, too. I don't know if it'll be worth or detrimental for smaller ones, too.  
  
The response needs to be a custom class rather than an alias for this to work though. BTW that's how mysql stores strings, because it allows memory reuse.
