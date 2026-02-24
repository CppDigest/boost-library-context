# #306 - Support PUNSUBSCRIBE [Closed]

> Username: anarthal  
> Created at: 2025-09-15 16:10:54 UTC  
> Updated at: 2025-10-24 22:41:50 UTC  
> Closed at: 2025-10-24 22:41:50 UTC  
> Url: https://github.com/boostorg/redis/issues/306  

It looks like PUBSUBSCRIBE (https://redis.io/docs/latest/commands/punsubscribe/) should be handled the same way as regular UNSUBSCRIBE.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-10-24 16:12:07 UTC  
> Url: https://github.com/boostorg/redis/issues/306#issuecomment-3443927282  

At the moment adding a PUBSUBSCRIBE command causes unspecified behavior (usually an exception difficult to read).
