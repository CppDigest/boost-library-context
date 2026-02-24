# #299 - Valkey: request client recommendation [Open]

> Username: anarthal  
> Created at: 2025-09-01 18:17:43 UTC  
> Updated at: 2025-09-05 17:27:28 UTC  
> Url: https://github.com/boostorg/redis/issues/299  

We need to fill [this JSON](https://github.com/valkey-io/valkey-doc/blob/main/clients/README.md) and probably open a PR against the repo.

---

## Comment 1

> Username: criatura2  
> Created at: 2025-09-02 07:30:25 UTC  
> Updated at: 2025-09-02 07:48:10 UTC  
> Url: https://github.com/boostorg/redis/issues/299#issuecomment-3244124057  

The _Advanced Features Overview_ gives us a good guide on things Boost.Redis could implement, more specifically  
  
- PubSub State Restoration: This would significantly reduce the complexity of [this](https://github.com/boostorg/redis/blob/2133ed747b246c5c9278c1c88a334d10ad4a0036/example/cpp20_subscriber.cpp#L61) loop.  
- Smart Backoff to Prevent Connection Storm: Not specially important for Servers that only have Boost.Redis connections since multiplexing reduces the number of connections to one or two per app. In my system the tcp backlog queue is 2048 for example.  
- Persistent Connection Pool: Not needed in Boost.Redis since it provides multiplexing which is even more efficient.  
  
We might also want to suggest a new column _Automatic pipelining_  as a distinctic feature to promote Boost.Redis. IIRC only the nodejs provided it too.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-09-05 17:27:28 UTC  
> Url: https://github.com/boostorg/redis/issues/299#issuecomment-3259215470  

@mzimbres what do you think of the PubSub State Restoration feature? We could have a `request` member in `config` with setup commands to be run after a successful connection, which would contain the SUBSCRIBE commands required by the user.
