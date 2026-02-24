# #444 - Shortest Path with max distance [Open]

> Username: andreacassioli  
> Created at: 2025-12-12 20:31:03 UTC  
> Updated at: 2026-01-04 20:24:59 UTC  
> Url: https://github.com/boostorg/graph/issues/444  

I am interested in finding the one-to-many shortest path only if it is below a certain max distance D. If a node is farther than D, the distance shall be inf, as usual from a non reachable node. I am considering non negative weights (maybe also just positive).  
  
My focus is mostly performance: I could run the full one-to-many search with Dijkstra and then simply post process the solution. But I expect that the node actually reachable within D are significantly less than the full graph.  
  
Despite being possible to use the RCSP, this seems to me a simple extension of Dijkstra that I wonder it could be achieved in BGL.   
  
My idea is would be to prevent a new node to be inserted in the queue if its distance from the source exceed D. From a quick glance at the BFS visit it does not seems possible to hack on that.  
  
Any suggestion?

---

## Comment 1

> Username: andreacassioli  
> Created at: 2025-12-28 21:22:04 UTC  
> Url: https://github.com/boostorg/graph/issues/444#issuecomment-3695059163  

It relate somehow to https://github.com/boostorg/graph/pull/13

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2025-12-29 04:02:30 UTC  
> Url: https://github.com/boostorg/graph/issues/444#issuecomment-3695372870  

It doesn't sound hard in principle, like, maybe just customizing the condition used for expanding nodes so that it cuts off at the desired distance.   
Might mean some refactoring. I haven't looked at this code for a while.

---

## Comment 3

> Username: andreacassioli  
> Created at: 2026-01-04 20:24:59 UTC  
> Url: https://github.com/boostorg/graph/issues/444#issuecomment-3708398195  

Thanks @jeremy-murphy I believe that it should be possible in the "no_color_map" version. I will look into it at a certain point.
