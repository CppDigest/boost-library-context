# #262 - Reduce latency of write operations [Open]

> Username: mzimbres  
> Created at: 2025-06-02 14:55:02 UTC  
> Updated at: 2025-06-03 13:35:12 UTC  
> Url: https://github.com/boostorg/redis/issues/262  

As described in [this](https://github.com/boostorg/redis/issues/246#issuecomment-2927909878) comment there is an easy way to reduce latency of write operations  
  
> We can avoid copying payloads [here](https://github.com/boostorg/redis/blob/0c8c6fcc095ff103e9adda708c1efb06fabfe08d/include/boost/redis/impl/multiplexer.ipp#L158) when the connection has only one ongoing `async_exec`. The multiplexer `get_write_buffer()` function could then return a `string_view` pointing directly to the request internal buffer instead of a copy of it, which would then be used [here](https://github.com/boostorg/redis/blob/0c8c6fcc095ff103e9adda708c1efb06fabfe08d/include/boost/redis/connection.hpp#L199).

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-06-03 13:35:11 UTC  
> Url: https://github.com/boostorg/redis/issues/262#issuecomment-2935281312  

Something would work here is to add a new option in `request::config::allow_pipeline` which would be `true` by default. If set to `false` the implementation would avoid copying as explained above.
