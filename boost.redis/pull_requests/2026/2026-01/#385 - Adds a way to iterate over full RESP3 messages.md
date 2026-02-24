# #385 Adds a way to iterate over full RESP3 messages [Closed]

> Username: anarthal  
> Created at: 2026-01-21 20:43:34 UTC  
> Updated at: 2026-01-27 17:19:21 UTC  
> Closed at: 2026-01-27 17:19:21 UTC  
> Url: https://github.com/boostorg/redis/pull/385  



---

## Comment 1

> Username: anarthal  
> Created_at: 2026-01-21 20:46:36 UTC  
> Url: https://github.com/boostorg/redis/pull/385#issuecomment-3781090444  

@mzimbres I'm trying to make working with RESP3 messages easier. What do you think of this? It's a forward range that allows you to consume a flat tree by breaking it into individual messages. It's much easier to use than iterating the raw range, and it should have the same performance.  
  
I've written this in an attempt to make processing pushes easier, as an alternative to #366.

---
