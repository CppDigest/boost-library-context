# #297 - Is `maximum_adjacency_search` broken? [Open]

> Username: sebrockm  
> Created at: 2022-07-11 22:49:38 UTC  
> Updated at: 2026-02-17 13:03:26 UTC  
> Url: https://github.com/boostorg/graph/issues/297  

There are several things wrong with `maximum_adjacency_search`:  
  
- The pseudo code on https://www.boost.org/doc/libs/1_79_0/libs/graph/doc/maximum_adjacency_search.html does not mention any weights. Yet, `weights` is a required parameter. It's unclear what it is doing.  
- In [L127](https://github.com/boostorg/graph/blob/430f61f6962614bb33fe7be3b054229953803ced/include/boost/graph/maximum_adjacency_search.hpp#L127), there is a `set` defined, but it is never filled with any values. In [L181](https://github.com/boostorg/graph/blob/430f61f6962614bb33fe7be3b054229953803ced/include/boost/graph/maximum_adjacency_search.hpp#L181) there is a loop iterating over this always empty `set`. This is dead code.  
- It looks like the `assignments` property map is a dead parameter. Looking at the implementation, in [L131](https://github.com/boostorg/graph/blob/430f61f6962614bb33fe7be3b054229953803ced/include/boost/graph/maximum_adjacency_search.hpp#L131) we see the one and only `put` operation performed on it. It overrides all possibly provided user input and assigns every vertex to itself. That makes all following `get` operations obsolete (every `get(assignments, x)` is equivalent to just `x`).  
- [L158](https://github.com/boostorg/graph/blob/430f61f6962614bb33fe7be3b054229953803ced/include/boost/graph/maximum_adjacency_search.hpp#L158) is a noop  
  
### Background:  
This algorithm was introduced in https://github.com/boostorg/graph/commit/393c072c186f6711d1537225dc116ea1528140ac. There, it was extracted from `stoer_wagner_min_cut` which caused #286.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-07-12 05:57:30 UTC  
> Url: https://github.com/boostorg/graph/issues/297#issuecomment-1181347316  

Interesting. Thanks for looking into it! You've certainly identified some serious issues with that implementation.  
  
The next big question would be, do the existing unit tests cover everything? If they do, then someone can be free to refactor the hell out of that implementation. But I can only imagine that it is going to require some additional tests to cover the obviously wrong behaviour on `assignments`, etc.  
  
The existing unit tests are moderately complicated; if someone wants to tackle this, I'd recommend adding some ultra-simple ones too.

---

## Comment 2

> Username: daankolthof  
> Created at: 2023-04-18 23:21:31 UTC  
> Updated at: 2023-04-19 11:59:02 UTC  
> Url: https://github.com/boostorg/graph/issues/297#issuecomment-1513904297  

Hey, I had a look at this as well. I'm not sure about the correctness of the pseudo-code/algorithm in the documentation (https://www.boost.org/doc/libs/1_82_0/libs/graph/doc/maximum_adjacency_search.html). Could either of you check whether this is correct?  
Based on the original research paper "A linear time 2 + epsilon approximation algorightm for edge connectivity" (as linked in the documentation), I came up with the following pseudocode, which does differ from the documentation:  
  
```  
nodes_visited = set()  
node_k_values = map<node, long>  
prio_queue = maxheap(start_node) // Maxheap/Priority Queue with start_node as only starting element.  
  
// The results of the algorithm, a list of nodes in order + k values for each edge  
node_order = []  
edge_k_values = map<edge, long>  
  
while prio_queue not empty {  
// Pop the node with highest k value  
// Then update k-values of neighbour nodes  
k_value, node = prio_queue.pop() // Get top element and remove from prio queue  
  
if nodes_visited.contains(top_node) {  
	continue  
}  
  
nodes_visited.add(top_node)  
  
node_order.push_back(top_node)  
  
for edge, neighour_node in top_node.neighbours() {  
  
	node_k_values[neighbour_node]++  
	edge_k_values[edge] = node_k_values[neighbour_node]  
	prio_queue.push_back(node_k_values[neighbour_node], neighbour_node)  
}  
}  
```  
  
Please let me know what you think. I am going to take a look at the unit tests and/or maybe write some tests myself to verify the above algorithm.

---

## Comment 3

> Username: daankolthof  
> Created at: 2023-04-19 15:32:48 UTC  
> Url: https://github.com/boostorg/graph/issues/297#issuecomment-1514945347  

Just to add to the above, it seems the implementation does what it's supposed to do. However, the pseudocode in the documentation doesn't match up with the original papers, nor does it match up with the implementation.  
If we can get the pseudocode clarified/rewritten, we might be able to refactor/simplify the implementation as well.

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2023-04-20 00:23:56 UTC  
> Url: https://github.com/boostorg/graph/issues/297#issuecomment-1515544646  

It took me a while of reading the documentation for this to realize that MAS is not an algorithm per se but another search strategy like BFS and DFS. That documentation could do with some nice graphs that show the sequence of the search, etc, and ultimately I would categorize this along with BFS and DFS in the table of contents, changing the category from "Core Searches" to just "Search". (As an aside "A* search" is not really a search, so it's fine where it is.)  
  
Anyway, the way forward is to make the unit tests watertight first so that then you can go to town changing the implementation. I wouldn't worry too much about the pseudocode for now.

---

## Comment 5

> Username: daankolthof  
> Created at: 2023-04-20 21:28:10 UTC  
> Url: https://github.com/boostorg/graph/issues/297#issuecomment-1516969094  

Great, I'm working on the unit tests right now. I'm expanding the existing ones and making new ones for simple and complex graphs.  
I'll try to have a PR ready before the end of next week.

---

## Comment 6

> Username: daankolthof  
> Created at: 2023-04-23 15:21:11 UTC  
> Url: https://github.com/boostorg/graph/issues/297#issuecomment-1519091840  

Hey @jeremy-murphy, @sebrockm (and others), I have created https://github.com/boostorg/graph/pull/333 which refactors the existing tests and creates many new ones. Please let me know what you think.

---

## Comment 7

> Username: daankolthof  
> Created at: 2023-04-23 15:24:19 UTC  
> Updated at: 2023-04-23 15:24:47 UTC  
> Url: https://github.com/boostorg/graph/issues/297#issuecomment-1519092461  

Also on the `weights` parameter: every edge in the graph contains a weight. In the simplest case, this can be 1 for every edge.  
MAS searching through the graph is done based on priority. The weights of each edge determine the priority of unvisited nodes. E.g., when node A is visited and edge A-B has priority 10, node B's priority in the queue will be increased by 10.  
(this can be useful to know later when we update the documentation)

---

## Comment 8

> Username: andreacassioli  
> Created at: 2025-11-25 21:16:07 UTC  
> Url: https://github.com/boostorg/graph/issues/297#issuecomment-3577620900  

I guess this issue is resolved, isn't it?

---

## Comment 9

> Username: jeremy-murphy  
> Created at: 2025-11-25 22:11:00 UTC  
> Url: https://github.com/boostorg/graph/issues/297#issuecomment-3577795338  

I'm not so sure. I just quickly went through the linked issues and pull requests, and it looks like the problems with MAS have been mitigated but not fixed. That is, we stopped using it as the basis of other algorithms, but MAS itself still needs fixing if we're ever going to use it. Anyone else able to confirm?

---

## Comment 10

> Username: andreacassioli  
> Created at: 2025-11-27 20:44:58 UTC  
> Url: https://github.com/boostorg/graph/issues/297#issuecomment-3587232975  

I can see at least issues 2, 3, and 4 are still there.

---

## Comment 11

> Username: jeremy-murphy  
> Created at: 2026-02-17 13:03:26 UTC  
> Url: https://github.com/boostorg/graph/issues/297#issuecomment-3914596556  

Anyone interested in doing more work on this?
