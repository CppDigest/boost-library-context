# #388 - PubSub state restorarion: UNSUBSCRIBE, PUNSUBSCRIBE without arguments [Open]

> Username: anarthal  
> Created at: 2026-01-27 13:42:18 UTC  
> Updated at: 2026-01-27 13:42:18 UTC  
> Url: https://github.com/boostorg/redis/issues/388  

I've just learnt that these are a thing. They cause the client to be unsubribed from everything they were subscribed to. We should support these in the PubSub state restoration algorithm.
