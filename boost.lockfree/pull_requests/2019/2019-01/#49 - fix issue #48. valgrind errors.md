# #49 fix issue #48. valgrind errors [Merged]

> Username: yuvalif  
> Created at: 2019-01-20 17:39:43 UTC  
> Updated at: 2019-02-21 04:02:24 UTC  
> Merged at: 2019-02-21 04:02:23 UTC  
> Closed at: 2019-02-21 04:02:24 UTC  
> Url: https://github.com/boostorg/lockfree/pull/49  

when running example or tests under valgrind, "uninitialized" valgrind error is not seen  
  
Signed-off-by: Yuval Lifshitz <yuvalif@yahoo.com>

---

## Review 1 [Changes requested]

> Username: timblechmann  
> Created_at: 2019-01-22 11:06:36 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/lockfree/pull/49#pullrequestreview-194948565  

this is kind of a false positive of valgrind: the ABA counter doesn't need to be initialised, we only need to make sure that it increments.  
but it won't do much harm to initialise it

📁 include/boost/lockfree/queue.hpp

```diff
 135 |-         atomic<tagged_node_handle> next;
 135 |         T data;
 136 |+         atomic<tagged_node_handle> next;
```

> Username: timblechmann  
> Created_at: 2019-01-22 11:04:44 UTC  
> Updated_at: 2019-01-22 15:15:13 UTC  
> Url: https://github.com/boostorg/lockfree/pull/49#discussion_r249734211  

the order was actually on purpose to ensure alignment of the `atomic`

> Username: yuvalif  
> Created_at: 2019-01-23 06:30:57 UTC  
> Url: https://github.com/boostorg/lockfree/pull/49#discussion_r250068888  

reverted it back


---

## Comment 2

> Username: yuvalif  
> Created_at: 2019-02-20 17:32:53 UTC  
> Url: https://github.com/boostorg/lockfree/pull/49#issuecomment-465678395  

@timblechmann any other fixes needed in the PR?

---
