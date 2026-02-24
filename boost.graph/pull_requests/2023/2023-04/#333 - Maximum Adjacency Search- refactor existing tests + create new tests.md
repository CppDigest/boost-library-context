# #333 Maximum Adjacency Search: refactor existing tests + create new tests [Closed]

> Username: daankolthof  
> Created at: 2023-04-23 15:18:33 UTC  
> Updated at: 2023-04-27 09:23:04 UTC  
> Closed at: 2023-04-27 09:13:20 UTC  
> Url: https://github.com/boostorg/graph/pull/333  

See https://github.com/boostorg/graph/issues/297

---

## Comment 1

> Username: daankolthof  
> Created_at: 2023-04-23 21:53:23 UTC  
> Url: https://github.com/boostorg/graph/pull/333#issuecomment-1519176875  

Blocked on https://github.com/boostorg/graph/issues/334.

---

## Review 2 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-04-24 00:50:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/333#pullrequestreview-1397052379  

📁 test/mas_test.cpp

```diff
  85 |+     const KeyedUpdatablePriorityQueue& m_pq_;
  86 |+     boost::shared_ptr< std::vector< vertex_descriptor > > vertex_visit_order_;
  87 |+     boost::shared_ptr< std::vector< weight_type > > vertex_weights_when_visited_;
```

> Username: jeremy-murphy  
> Created_at: 2023-04-24 00:50:34 UTC  
> Updated_at: 2023-04-24 00:50:35 UTC  
> Url: https://github.com/boostorg/graph/pull/333#discussion_r1174692324  

Do these have to be `shared_ptr`? I don't have time to look at the full code, but I couldn't see a reason for it here in the PR code.

> Username: daankolthof  
> Created_at: 2023-04-24 08:39:22 UTC  
> Updated_at: 2023-04-24 08:46:14 UTC  
> Url: https://github.com/boostorg/graph/pull/333#discussion_r1174964066  

These are `shared_ptr`'s because the visitor object is passed by value during the call to `maximum_adjacency_search`. Apparently this passing by value is 'normal' as I'm also seeing it in BFS.  
  
In DFS however, it's passed by reference, which makes more sense to me.  
Changing this for MAS (and maybe BFS) could be a separate future work item.

> Username: jeremy-murphy  
> Created_at: 2023-04-27 04:27:56 UTC  
> Url: https://github.com/boostorg/graph/pull/333#discussion_r1178611348  

Ah, I see. Yeah, I think those algorithms should probably take the visitor by value _and_ return it by value.


---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-04-24 06:55:46 UTC  
> Url: https://github.com/boostorg/graph/pull/333#issuecomment-1519488976  

Looks like the build failed on something unrelated to your code. Can you trigger a rebuild?

---

## Comment 4

> Username: daankolthof  
> Created_at: 2023-04-24 08:45:15 UTC  
> Url: https://github.com/boostorg/graph/pull/333#issuecomment-1519641733  

> Looks like the build failed on something unrelated to your code. Can you trigger a rebuild?  
  
Only by pushing a new commit, I don't have any access to re-trigger the failed build. Do you have access?

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2023-04-27 04:23:40 UTC  
> Url: https://github.com/boostorg/graph/pull/333#issuecomment-1524677313  

> > Looks like the build failed on something unrelated to your code. Can you trigger a rebuild?  
>   
> Only by pushing a new commit, I don't have any access to re-trigger the failed build. Do you have access?  
  
I never figured out if it was possible. But now that I've merged the fix for Bionic, you can merge that change in and push it.

---

## Comment 6

> Username: daankolthof  
> Created_at: 2023-04-27 09:22:57 UTC  
> Updated_at: 2023-04-27 09:23:04 UTC  
> Url: https://github.com/boostorg/graph/pull/333#issuecomment-1525243366  

I've switched some branches around on my fork, new PR is at https://github.com/boostorg/graph/pull/337.

---
