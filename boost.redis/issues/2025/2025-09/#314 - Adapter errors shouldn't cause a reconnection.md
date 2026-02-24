# #314 - Adapter errors shouldn't cause a reconnection [Open]

> Username: anarthal  
> Created at: 2025-09-22 14:34:19 UTC  
> Updated at: 2025-11-19 09:36:31 UTC  
> Url: https://github.com/boostorg/redis/issues/314  

At some point, I had in a test  
  
```cpp  
request req;  
req.push("BLPOP", "any", 3);  
  
conn.async_exec(req, ignore, ...);  
```  
  
Redis responds with a NULL if the BLPOP times out, which causes `ignore` to issue an error, and that error gets propagated and cancels the connection. I don't think this is the best we can do - the connection is healthy and shouldn't be torn down here.  
  
When parsing, I think we should distinguish between protocol violation errors (e.g. an invalid RESP3 message), which should cause re-connection; and adapter errors, which should make the request fail, but shouldn't cause re-connection. For this to work, we probably need to make adapters resilient to getting nodes after an error has occurred.

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-11-19 09:36:31 UTC  
> Url: https://github.com/boostorg/redis/issues/314#issuecomment-3551746954  

> I don't think this is the best we can do - the connection is healthy and shouldn't be torn down here.  
  
Agree.  
  
> When parsing, I think we should distinguish between protocol violation errors (e.g. an invalid RESP3 message), which should cause re-connection; and adapter errors, which should make the request fail, but shouldn't cause re-connection. For this to work, we probably need to make adapters resilient to getting nodes after an error has occurred.  
  
Protocols violations are never passed to adapters, instead they are communicated to the callers until it eventually reaches the `reader_op`. This happens here https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/include/boost/redis/resp3/parser.hpp#L98-L109  
  
I think we can even consider this as a bug and not a breaking change. It would be enough to remove this scope https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/include/boost/redis/adapter/ignore.hpp#L25-L34
