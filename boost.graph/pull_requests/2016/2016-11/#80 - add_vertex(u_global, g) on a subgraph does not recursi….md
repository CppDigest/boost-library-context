# #80 [subgraph.hpp] add_vertex(u_global, g) on a subgraph does not recursi… [Merged]

> Username: j-4  
> Created at: 2016-11-02 10:04:59 UTC  
> Updated at: 2018-10-12 17:32:21 UTC  
> Merged at: 2018-10-12 17:32:20 UTC  
> Closed at: 2018-10-12 17:32:20 UTC  
> Url: https://github.com/boostorg/graph/pull/80  

…vely add to parent subgraphs  
  
Hello,  
One of the invariants of an induced subgraph is:  
- If vertex u is in subgraph g, then u must be in g.parent().  
  
This is true if you call add_vertex(g) on a subgraph to create a new vertex.  
If you however choose to add a already existing global vertex to the subgraph, this addition is not propagated to the parents of this subgraph.  
  
 I assume this is a bug and fixed it.   
Bug Ticket can be found here: https://svn.boost.org/trac/boost/ticket/11714  
Thanks, Stefan

---

## Comment 1

> Username: j-4  
> Created_at: 2018-03-08 15:55:41 UTC  
> Updated_at: 2018-03-08 15:56:51 UTC  
> Url: https://github.com/boostorg/graph/pull/80#issuecomment-371530320  

Is anyone still maintaining this?  
@Belcourt maybe?

---

## Comment 2

> Username: anadon  
> Created_at: 2018-08-31 18:23:21 UTC  
> Url: https://github.com/boostorg/graph/pull/80#issuecomment-417750876  

I'm helping out with the PR backlog. Looks like you have a bugfix, test, and no new examples will be needed. This will be in the first batch of merged PR's. This is to let you know and help me prioritize PR's.

---
