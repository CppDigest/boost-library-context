# #75 Dijkstra SPP can take multiple start vertices. [Open]

> Username: mfuchs  
> Created at: 2016-08-29 19:40:49 UTC  
> Updated at: 2019-03-16 12:02:14 UTC  
> Url: https://github.com/boostorg/graph/pull/75  

Instead of a single start vertex a range of start vertices can be handed in.  
The range is modeled by two iterators s_begin and s_end.  
  
The reason to use iterators is to be consistent with dijkstra_shortest_path  
that already supports multiple start vertices for some functions.  
Moreover, another advantage of iterators is backwards compatibility with  
the existing functions since the parameter count is different.

---

## Comment 1

> Username: e-kwsm  
> Created_at: 2016-08-30 11:31:29 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-243410960  

Just for information. Boost already has [`floyd_warshall_all_pairs_shortest_paths`](http://www.boost.org/doc/libs/release/libs/graph/doc/floyd_warshall_shortest.html), which finds the shortest paths between ALL pairs of vertices and has complexity of _O_(_V_^3);  
current `dijkstra_shortest_paths` has complexity of _O_(_V_ log _V_ + _E_).

---

## Comment 2

> Username: mfuchs  
> Created_at: 2016-08-30 16:18:02 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-243494787  

Thanks for the information and the reply  
Floyd-Warshall fits a different use case than mine.  
  
I am interested in the shortest path of any of [s_start, s_end) to some end points.  
I am not interested what the shortest paths of s_1, s_2, ..., s_m to x_1, x_2, ..., x_n are.  
Instead I am interested in min(spp(s_1, x_i), spp(s_2, x_i), ..., spp(s_n, x_i)) for all i in 1 to n.  
My adaptions to Dijkstra SPP do that in O(V log V + E).  
  
In work I could use these changes.  
At the moment I have to call `dijkstra_shortest_paths` multiple times and "merge" the results.  
So my code is more complex and slower than needed.

---

## Comment 3

> Username: Belcourt  
> Created_at: 2016-11-01 02:02:54 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-257472440  

While this seems to build and run okay, I'm a bit reluctant to break the existing dijkstra_shortest_paths  API.  Can you refactor this to add a new overloaded method that takes the iterator pair, and leave the single vertex descriptor implemenation unmodified?  Perhaps you considered this already, in which case could you explain why breaking the existing API is preferable to adding a new overloaded dijkstra_shortest_paths?

---

## Comment 4

> Username: mfuchs  
> Created_at: 2016-11-01 13:08:04 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-257562006  

Thanks for your reply.  
I tried to make sure not to break the public API by having function pairs: One taking a single vertex, the other taking iterators.  
To avoid code duplication the single vertex versions internally forward to their iterator counterparts.  
Maybe I have overlooked something, could you please tell me in which case I broke the API?

---

## Review 5 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-07-25 10:22:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/75#pullrequestreview-140249932  

📁 include/boost/graph/dijkstra_shortest_paths.hpp

```diff
 627 |+     dijkstra_shortest_paths(g, &s, &s + 1, params);
 628 |+   }
 629 |+ 
```

> Username: jeremy-murphy  
> Created_at: 2018-07-25 10:22:22 UTC  
> Updated_at: 2019-03-16 12:01:22 UTC  
> Url: https://github.com/boostorg/graph/pull/75#discussion_r205057474  

@Belcourt I think this is the overload that you might have overlooked that preserves the existing interface.


---

## Comment 6

> Username: anadon  
> Created_at: 2018-08-31 18:30:12 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-417752727  

I'm helping out with the PR backlog. Looks like you have a feature addition, test, but no new examples or documentation.  I would like you to add documentation and examples. This is to let you know and help me prioritize PR's.

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2018-10-14 02:41:52 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-429591613  

@mfuchs you need to rebase your branch.

---

## Comment 8

> Username: anadon  
> Created_at: 2018-10-15 19:52:00 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-429989940  

I tried rebasing locally and it needs more work than I can put in in the midst of coursework.

---

## Comment 9

> Username: anadon  
> Created_at: 2018-11-01 14:14:43 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-435053480  

@jzmaddock The original author has been MIA for a while.  Should this be closed?

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2018-11-01 18:26:37 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-435138619  

@jeremy-murphy, @Belcourt, any thoughts on this one?

---

## Comment 11

> Username: mfuchs  
> Created_at: 2018-11-01 21:29:33 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-435193842  

I might have time the following weeks to look at this.

---

## Comment 12

> Username: jeremy-murphy  
> Created_at: 2018-11-04 00:38:31 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-435632230  

> @jeremy-murphy, @Belcourt, any thoughts on this one?  
  
I think this is a neat extension to the classic single-source definition of Dijkstra's algorithm.  
  
There is nothing wrong with the programming API as Belcourt believed.  
  
Because it breaks the intuitive and documented single-source definition of Dijkstra's algorithm, it needs explanation and examples in the documentation.  
  
If there is a published paper on this extension to single-source Dijkstra's algorithm then that would be a useful link to provide too.

---

## Comment 13

> Username: jeremy-murphy  
> Created_at: 2019-01-30 09:49:28 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-458880394  

- [ ] documentation  
- [ ] examples  
  
Sorry, just experimenting with this Task List feature. But also I think this feature is worth pursuing.  :)

---

## Comment 14

> Username: mfuchs  
> Created_at: 2019-03-16 12:02:14 UTC  
> Url: https://github.com/boostorg/graph/pull/75#issuecomment-473524154  

@jeremy-murphy sorry it took me ages to update it, but now I finally got around it. I have also provided documentation and examples.

---
