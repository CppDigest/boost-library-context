# #432 Update r_c_shortest_paths.html [Merged]

> Username: andrea-cassioli-maersk  
> Created at: 2025-07-10 13:32:28 UTC  
> Updated at: 2025-07-18 03:08:05 UTC  
> Merged at: 2025-07-18 03:08:04 UTC  
> Closed at: 2025-07-18 03:08:04 UTC  
> Url: https://github.com/boostorg/graph/pull/432  

Add few details about the on_enter_loop visitor event that is not documented.

---

## Review 1 [Commented]

> Username: jeremy-murphy  
> Created_at: 2025-07-16 22:44:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/432#pullrequestreview-3027124021  

📁 doc/r_c_shortest_paths.html

```diff
 330 | <tr>
 331 | <td>
 332 |+ <tt>vis.on_enter_loop(unprocessed_labels, const Graph&amp; g )</tt>
```

> Username: jeremy-murphy  
> Created_at: 2025-07-16 22:44:51 UTC  
> Url: https://github.com/boostorg/graph/pull/432#discussion_r2211753460  

Shouldn't it rather be this?  
```diff  
-<tt>vis.on_enter_loop(unprocessed_labels, const Graph&amp; g )</tt>  
+<tt>vis.on_enter_loop(const Queue&amp; queue, const Graph&amp; g )</tt>  
```  
It looks like you accidentally mixed arguments and parameters together, but I could be wrong.

> Username: andrea-cassioli-maersk  
> Created_at: 2025-07-17 09:26:03 UTC  
> Url: https://github.com/boostorg/graph/pull/432#discussion_r2212825671  

You are right @jeremy-murphy , I was unsure about the convention in the docs and which type to use.


---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2025-07-18 03:07:59 UTC  
> Url: https://github.com/boostorg/graph/pull/432#issuecomment-3086540981  

Thank you!

---
