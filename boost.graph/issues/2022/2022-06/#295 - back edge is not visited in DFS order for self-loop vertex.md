# #295 - back edge is not visited in DFS order for self-loop vertex [Closed]

> Username: Inori  
> Created at: 2022-06-13 20:11:06 UTC  
> Updated at: 2022-06-14 07:18:48 UTC  
> Closed at: 2022-06-14 06:56:21 UTC  
> Url: https://github.com/boostorg/graph/issues/295  

When calling `boost::depth_first_search` on a graph which has one or more self-loop vertices, the `back_edge` callback will not get called in DFS order, seems it will be called when all other non-self-loop back edges are visited, this add some inconvenience.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-06-13 23:49:56 UTC  
> Url: https://github.com/boostorg/graph/issues/295#issuecomment-1154558388  

Why is it inconvenient?  
  
It could be something to do with how the graph is constructed. Are the self loops always the last edge created from that particular source vertex? If possible, try creating them first?  
  
It doesn't sound like incorrect behaviour, or is there something I'm missing?

---

## Comment 2

> Username: Inori  
> Created at: 2022-06-14 03:03:27 UTC  
> Updated at: 2022-06-14 07:18:48 UTC  
> Url: https://github.com/boostorg/graph/issues/295#issuecomment-1154657153  

Ok, it seems there is some misunderstanding for myself.  
It seems there's no guarantees on the order of `back_edge` calling from the api document.  
  
In fact, I'm trying to implement a compiler.  
The visit order of self-loop back edge seems unusual, not like non-self-loop back edges, of which the edge target and back edge follows first-in-last-out stack order, the self-loop back edge is visited after all other normal back edges are visited, making implementation of scope stack not that naturally.  
I can achieve what I expect with current behavior though, just need more steps.
