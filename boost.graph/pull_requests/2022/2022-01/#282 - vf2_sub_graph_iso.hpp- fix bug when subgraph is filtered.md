# #282 vf2_sub_graph_iso.hpp: fix bug when subgraph is filtered [Open]

> Username: count0  
> Created at: 2022-01-02 15:59:20 UTC  
> Updated at: 2023-09-13 01:19:55 UTC  
> Url: https://github.com/boostorg/graph/pull/282  

This fixes a bug when num_vertices() returns a value that does not match  
the actual number of vertices. This happens for instances of  
filtered_graph<>.  
  
The fix involves looping over the graph to count the actual number of  
vertices. The performance overhead is negligible.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2022-01-03 23:07:11 UTC  
> Url: https://github.com/boostorg/graph/pull/282#issuecomment-1004416138  

Thanks for contributing a fix. Could I ask you to please also add a small unit test that demonstrates the new functionality? (I.e. the test should fail without your changes.)  
  
I don't know the details of this algorithm -- do we only have to check if one graph is filtered, and not the other one?

---

## Review 2 [Changes requested]

> Username: jakobandersen  
> Created_at: 2022-01-04 08:03:05 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/282#pullrequestreview-843283044  

📁 include/boost/graph/vf2_sub_graph_iso.hpp

```diff
  82 |+         BGL_FORALL_VERTICES_T(v, g, Graph)
  83 |+             N++;
  84 |+         return N;
```

> Username: jakobandersen  
> Created_at: 2022-01-04 08:01:56 UTC  
> Updated_at: 2022-01-04 08:03:05 UTC  
> Url: https://github.com/boostorg/graph/pull/282#discussion_r777886298  

```diff  
-        return N;  
+        typedef typename graph_traits<Graph>::vertex_iterator iter;  
+        std::pair<iter, iter> vs = vertices(g);  
+        return std::distance(vs.first, vs.second);  
```  
The old ``num_vertices()`` takes constant time, so having a raw loop which forces linear time is not ok. With ``std::distance()`` it should only take linear time when needed (e.g., for ``filtered_graph``).

> Username: count0  
> Created_at: 2022-01-04 08:09:24 UTC  
> Updated_at: 2022-01-04 08:09:25 UTC  
> Url: https://github.com/boostorg/graph/pull/282#discussion_r777889777  

This is a good suggestion!


---

## Comment 3

> Username: jakobandersen  
> Created_at: 2022-01-04 08:09:06 UTC  
> Url: https://github.com/boostorg/graph/pull/282#issuecomment-1004600797  

> I don't know the details of this algorithm -- do we only have to check if one graph is filtered, and not the other one?  
  
Both graphs can be potentially be filtered, but I think the PR covers it. The check in ``success()`` is for whether all the vertices in the "small" graph has been mapped, and with the initial vertex count checks that is enough.

---

## Comment 4

> Username: count0  
> Created_at: 2022-01-04 08:17:55 UTC  
> Url: https://github.com/boostorg/graph/pull/282#issuecomment-1004605746  

> Thanks for contributing a fix. Could I ask you to please also add a small unit test that demonstrates the new functionality? (I.e. the test should fail without your changes.)  
  
This problem has been noticed in graph-tool (which is built on top of BGL), for which there is a simple example where the code fails: https://git.skewed.de/count0/graph-tool/-/issues/715  
  
If necessary I could translate it to a minimal C++ code.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2022-02-07 23:12:42 UTC  
> Url: https://github.com/boostorg/graph/pull/282#issuecomment-1032030264  

> If necessary I could translate it to a minimal C++ code.  
  
Yes, please, that is what I would like.  
  
The `develop` branch appears to be green now, so we can actually merge this when it's done.

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2022-03-02 15:34:59 UTC  
> Url: https://github.com/boostorg/graph/pull/282#issuecomment-1057065659  

@count0 do you have time to address the issues I commented on and add the unit test?

---
