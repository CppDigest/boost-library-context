# #222 - [Q] `finish_edge` in non-recursive DFS [Open]

> Username: qbit86  
> Created at: 2020-06-07 15:29:22 UTC  
> Updated at: 2024-10-09 03:34:04 UTC  
> Url: https://github.com/boostorg/graph/issues/222  

Could you please clarify implementation of directed and undirected DFS, non-recursive ones?  
  
Take a look at these lines: [depth_first_search.hpp#L173..L178](https://github.com/boostorg/graph/blob/ca17604dca362e0378ca14c7e8cf9a57a0c887ad/include/boost/graph/depth_first_search.hpp#L173..L178)  
  
Here is comment:    
> `finish_edge` has to be called here, not after the loop. Think of the pop as the return from a recursive call.  
  
But in undirected DFS this event is raised after the loop: [undirected_dfs.hpp#L104..L105](https://github.com/boostorg/graph/blob/ca17604dca362e0378ca14c7e8cf9a57a0c887ad/include/boost/graph/undirected_dfs.hpp#L104..L105)  
  
Why it's different in this case?

---

## Comment 1

> Username: qbit86  
> Created at: 2020-06-08 15:06:34 UTC  
> Url: https://github.com/boostorg/graph/issues/222#issuecomment-640688903  

To make this issue more like feature request than forum-like question: please, add pseudocode for non-recursive version of DFS on these documentation pages:    
+ [depth_first_search.html](https://www.boost.org/doc/libs/1_73_0/libs/graph/doc/depth_first_search.html)  
+ [undirected_dfs.html](https://www.boost.org/doc/libs/1_73_0/libs/graph/doc/undirected_dfs.html)

---

## Comment 2

> Username: qbit86  
> Created at: 2020-06-13 14:01:50 UTC  
> Url: https://github.com/boostorg/graph/issues/222#issuecomment-643627944  

Speaking on directed and undirected searches applied to _undirected_ graphs.  
  
Why do you need a separate undirected version of DFS, but don't need a special version of undirected BFS? What's wrong with running directed `depth_first_search()` on undirected graph? If there are some reasons — why the same reasons don't apply to `breadth_first_search()`?  
  
In `undirected_dfs()` you have a color map for tracking edges, so the algorithm does not emit events for inverted edges. Why then don't you add undirected version of BFS with the same property? So less `non_tree_edge` would be emitted for inverted edges.

---

## Comment 3

> Username: qbit86  
> Created at: 2020-06-13 14:16:44 UTC  
> Url: https://github.com/boostorg/graph/issues/222#issuecomment-643629722  

Another question about the design of BFS. Why do you need all three events: `non_tree_edge`, `gray_target`, `black_target`? A pair of `non_tree_edge_with_gray_target` and `non_tree_edge_with_black_target` looks sufficient, isn't it?  
  
And one more question, on naming. I've looked through a dozen of books on graph algorithms, and never met there a notion of “source” and “target” applying to endpoints of an edge; only “tail” and “head”. Why did you decide to call them so (which conflicts with conventional names for start and finish vertices of the path)?

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2024-10-09 03:34:02 UTC  
> Url: https://github.com/boostorg/graph/issues/222#issuecomment-2401200812  

Good questions. I don't have the answers.  :) Chances are that the original authors just overlooked it.
