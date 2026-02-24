# #233 - astar_search: visitor edge_relaxed method called inconsistently before or after cost map is set [Open]

> Username: risicle  
> Created at: 2020-12-06 16:20:59 UTC  
> Updated at: 2023-05-07 23:55:40 UTC  
> Url: https://github.com/boostorg/graph/issues/233  

`astar_bfs_visitor` calls `m_vis.edge_relaxed` _before_ setting the new cost map value in `tree_edge` and `black_target` (and in `astar_search_no_init_tree`'s main loop from the looks of it), but _afterwards_ in `gray_target`. This is very confusing if you want to look up the new cost value _in_ your visitor method.

---

## Comment 1

> Username: qbit86  
> Created at: 2021-06-01 09:59:40 UTC  
> Url: https://github.com/boostorg/graph/issues/233#issuecomment-851996894  

Let me attach the code for clarity.  
  
[tree_edge](https://github.com/boostorg/graph/blob/d865589d62afba7ca590bfcaf76f0723f8494dd4/include/boost/graph/astar_search.hpp#L185...L188)  
```cpp  
m_vis.edge_relaxed(e, g);  
put(m_cost, target(e, g),  
    m_combine(  
        get(m_distance, target(e, g)), m_h(target(e, g))));  
```  
  
[gray_target](https://github.com/boostorg/graph/blob/d865589d62afba7ca590bfcaf76f0723f8494dd4/include/boost/graph/astar_search.hpp#L203...L207)  
```cpp  
put(m_cost, target(e, g),  
    m_combine(  
        get(m_distance, target(e, g)), m_h(target(e, g))));  
m_Q.update(target(e, g));  
m_vis.edge_relaxed(e, g);  
```  
  
[black_target](https://github.com/boostorg/graph/blob/d865589d62afba7ca590bfcaf76f0723f8494dd4/include/boost/graph/astar_search.hpp#L222...L228)  
```cpp  
m_vis.edge_relaxed(e, g);  
put(m_cost, target(e, g),  
    m_combine(  
        get(m_distance, target(e, g)), m_h(target(e, g))));  
m_Q.push(target(e, g));  
put(m_color, target(e, g), Color::gray());  
m_vis.black_target(e, g);  
```  
  
At the moment of `edge_relaxed` event:  
- the predecessor map and the distance map are already updated;  
- the color map is not updated yet;  
- the queue and the cost map are inconsistently updated.  
  
The queue should probably be updated after `edge_relaxed`, to be consistent with “outer” [breadth_first_visit](https://github.com/boostorg/graph/blob/d865589d62afba7ca590bfcaf76f0723f8494dd4/include/boost/graph/breadth_first_search.hpp#L90...L93).  
The cost map should almost certainly be updated before `edge_relaxed`.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2023-05-07 23:55:40 UTC  
> Url: https://github.com/boostorg/graph/issues/233#issuecomment-1537572194  

Could one of you make a PR to fix it? @qbit86 I think you already have the code ready?  :)
