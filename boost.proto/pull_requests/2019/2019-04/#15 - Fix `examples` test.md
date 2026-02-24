# #15 Fix `examples` test [Merged]

> Username: Kojoley  
> Created at: 2019-04-01 22:00:57 UTC  
> Updated at: 2019-04-05 22:43:41 UTC  
> Merged at: 2019-04-05 22:42:55 UTC  
> Closed at: 2019-04-05 22:42:55 UTC  
> Url: https://github.com/boostorg/proto/pull/15  

It has two issues, one regarding MPL, and other about breaking Fusion invariant.  
  
Tested on MSVC 9/10/11/14.0/14.1, Clang 7, GCC 8.  
  
Fixes #5

---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-04-05 20:51:07 UTC  
> Url: https://github.com/boostorg/proto/pull/15#issuecomment-480417876  

Ping

---

## Review 2 [Commented]

> Username: ericniebler  
> Created_at: 2019-04-05 21:17:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/proto/pull/15#pullrequestreview-223483140  

📁 test/examples.cpp

```diff
 183 |       front. >>*/
 184 |-       , proto::reverse_fold<
 184 |+       , proto::fold<
```

> Username: ericniebler  
> Created_at: 2019-04-05 21:17:22 UTC  
> Updated_at: 2019-04-05 21:26:42 UTC  
> Url: https://github.com/boostorg/proto/pull/15#discussion_r272746753  

Change the comment to agree with the implementation, please.

> Username: Kojoley  
> Created_at: 2019-04-05 21:27:09 UTC  
> Url: https://github.com/boostorg/proto/pull/15#discussion_r272749204  

Done


---
