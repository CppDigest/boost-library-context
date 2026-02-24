# #13     dijkstra_shortest_paths_no_init supports pruning on distances [Open]

> Username: wygos  
> Created at: 2014-07-23 10:55:43 UTC  
> Updated at: 2025-12-29 01:20:06 UTC  
> Url: https://github.com/boostorg/graph/pull/13  

```  
When the distance map is given and the newly discovered distance  
is worse than the distance stored in the distance map,  
the vertex is not even added to the priority queue.  
```

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-11-02 21:07:27 UTC  
> Url: https://github.com/boostorg/graph/pull/13#issuecomment-61423628  

I'm not sure about this.  It seems to me that if the distance decreases, line 145 of dijkstra_shortest_path.hpp, then there's a call to dijkstra_queue_update() which certainly appears to update the queue (m_Q).  Perhaps I'm not clear on what you're trying to achieve but the priority queue does seem to be updated when the distance decreases.  Can you check this again and give more details if you still believe it's broken?

---

## Comment 2

> Username: wygos  
> Created_at: 2014-11-12 12:59:43 UTC  
> Updated_at: 2015-10-20 12:42:49 UTC  
> Url: https://github.com/boostorg/graph/pull/13#issuecomment-62714130  

The main issue is to make dijkstra_shortest_paths_no_init support more efficient.  
This is done by pruning the vertices, that are never going to be improved.  
  
Example:  
  
Assume my graph is as follows:  
  
1 --- 2 --- 3 --- 4 --- 5 --- 6  
  
Assume that weights of all edges equals 1, also assume that we run dijkstra_shortest_paths_no_init, starting from vertex number 1 and we provide the following distance map:  
  
1 -> 0  
2 -> infinity  
3 -> infinity  
4 -> 1  
5 -> 1  
6 -> 1  
  
Vertices 1,2,3 are initialized in the standard way, but vertices are initialized with smaller values.  
In this case dijkstra_shortest_paths_no_init supports should never visit vertex number 4 since the corresponding shortest path weight equals 2 > 1.  
The vertices 5  and 6 shouldn't be even discovered.   
  
If one'd like to see a natural use case, please see this algorithm:  
http://paal.mimuw.edu.pl/do_vv_2kminus1.html  
(now, in order to gain performance, the nasty workaround is used in this code)  
  
The second motivation for this change is that dijkstra is NOT BFS. It was impemented this way using not very intuitive vistor patern usage.  
Maybe BFS could be seen as special case of dijkstra with appropriate queue, but not the other way around (current version).

---

## Comment 3

> Username: anadon  
> Created_at: 2018-08-31 18:34:47 UTC  
> Url: https://github.com/boostorg/graph/pull/13#issuecomment-417753935  

I'm helping out with the PR backlog. Looks like you have a code change, no test, and no new examples will be needed.  This is very old and I do not know if it is still relevant.  Could I get @wygos to chime in?  This is to let you know and help me prioritize PR's.

---

## Comment 4

> Username: wygos  
> Created_at: 2018-08-31 19:46:21 UTC  
> Url: https://github.com/boostorg/graph/pull/13#issuecomment-417771111  

Hi, I'm happy to hear that your working on it! It is still relevant. In some special cases, the algorithm is inefficient. There was a discussion in the boost mailing list:  
  
https://lists.boost.org/Archives/boost/2015/10/226337.php  
  
and the consensus was, that the patch should be accepted.  
  
Regards,  
  
Piotr

---

## Comment 5

> Username: anadon  
> Created_at: 2018-08-31 19:48:24 UTC  
> Url: https://github.com/boostorg/graph/pull/13#issuecomment-417771534  

Since this looks more like a feature, I'll try to include it in the second batch of merges.  Thanks!

---

## Comment 6

> Username: anadon  
> Created_at: 2018-10-15 19:34:15 UTC  
> Url: https://github.com/boostorg/graph/pull/13#issuecomment-429984650  

@wygos Can you rebase your PR on devel?

---

## Comment 7

> Username: wygos  
> Created_at: 2018-10-22 08:48:42 UTC  
> Url: https://github.com/boostorg/graph/pull/13#issuecomment-431776109  

> @wygos Can you rebase your PR on devel?  
  
DONE. Note that github shows incorrect diffs but after fetching changes everything is OK.

---

## Comment 8

> Username: anadon  
> Created_at: 2018-11-01 14:16:52 UTC  
> Url: https://github.com/boostorg/graph/pull/13#issuecomment-435054114  

@wygos Upstream devel has TravisCI tests which your is missing.  Your tests are also failing.  Please rebase.

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2025-12-29 01:20:06 UTC  
> Url: https://github.com/boostorg/graph/pull/13#issuecomment-3695220881  

This was just brought to my attention. I'll try to have a look over the holidays, no promises though.

---
