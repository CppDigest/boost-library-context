# #73 add iterator feature and relative tests [Closed]

> Username: WadeGao  
> Created at: 2022-09-11 14:39:02 UTC  
> Updated at: 2025-09-09 14:52:07 UTC  
> Closed at: 2025-08-18 15:41:36 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/73  

I would like to support iterator feature to make it safer when traversing a dynamic_bitset and make the corresponding snippets acting like STL.   
  
I think it's necessary for dynamic_bitset to have this feature through my thinking and other developer's opinions (like this: https://m.weibo.cn/5027267375/4757345970424490).  
  
Thank you!

---

## Comment 1

> Username: gennaroprota  
> Created_at: 2025-08-01 14:13:17 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/73#issuecomment-3144734439  

Did you read [the rationale](https://github.com/boostorg/dynamic_bitset/blob/2da0cd50140894a762eb296d69c51a926c4df86e/doc/modules/ROOT/pages/index.adoc)?

---

## Comment 2

> Username: gennaroprota  
> Created_at: 2025-09-09 14:52:06 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/73#issuecomment-3271090919  

Following a discussion on the mailing list, we decided to implement C++20 iterators. The code of `dynamic_bitset` had changed too much to use your PR, so I re-implemented them from scratch.

---
