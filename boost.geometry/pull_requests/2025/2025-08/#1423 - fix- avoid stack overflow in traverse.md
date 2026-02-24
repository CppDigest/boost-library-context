# #1423 fix: avoid stack overflow in traverse [Merged]

> Username: barendgehrels  
> Created at: 2025-08-01 16:50:11 UTC  
> Updated at: 2025-08-04 15:55:42 UTC  
> Merged at: 2025-08-04 05:53:13 UTC  
> Closed at: 2025-08-04 05:53:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1423  

See #1411 (last commit) where it was discovered and fixed.  
This commit is meant to be merged to `1.89` beta

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-08-01 16:51:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1423#pullrequestreview-3079922093  

📁 include/boost/geometry/algorithms/detail/overlay/graph/traverse_graph.hpp

```diff
 189 |+         // The iteration count is a defensive check to prevent endless loops and not iterate
 190 |+         // more than times there are turns (this should not happen).
 191 |+         while (iteration_count < m_turns.size())
```

> Username: barendgehrels  
> Created_at: 2025-08-01 16:51:07 UTC  
> Updated_at: 2025-08-01 16:51:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1423#discussion_r2248431297  

Best reviewing it with **Hide whitespace**.  
Then you can see it just changes the recursive behaviour to a loop.


---

## Review 2 [Approved]

> Username: tinko92  
> Created_at: 2025-08-02 05:38:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1423#pullrequestreview-3080927037  

Looks good to me (the hide whitespace is very helpful) and adds no failures or warnings to the test suite run afaics.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2025-08-04 05:59:06 UTC  
> Updated_at: 2025-08-04 05:59:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1423#issuecomment-3149226685  

Per mail today  
  
>Calendar is here: https://www.boost.org/calendar  
  
> Current status of the master branch:  
>	Master is open for bug fixes and documentation updates. Other changes by permission of a release manager.  
> The release managers  
  
@vissarion @tinko92 I will merge this to our master branch tonight or tomorrow. It is open until August 6

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2025-08-04 15:55:05 UTC  
> Updated_at: 2025-08-04 15:55:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1423#issuecomment-3151328073  

@vissarion @tinko92 this is cherry-picked into the master branch:   
https://github.com/boostorg/geometry/commit/617d75e0cda78b1f05590319200973514b609097

---
