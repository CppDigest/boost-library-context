# #45 [subgraph.hpp] add_vertex(u_global, g) on a subgraph does not recursi… [Closed]

> Username: j-4  
> Created at: 2015-10-07 13:52:37 UTC  
> Updated at: 2016-11-02 10:06:51 UTC  
> Closed at: 2016-11-02 10:06:28 UTC  
> Url: https://github.com/boostorg/graph/pull/45  

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

> Username: murraycu  
> Created_at: 2016-08-12 07:50:45 UTC  
> Url: https://github.com/boostorg/graph/pull/45#issuecomment-239383234  

If I were the maintainer (I'm not), I would prefer these commits to be squashed for easier reviewing.

---

## Comment 2

> Username: murraycu  
> Created_at: 2016-08-12 07:52:11 UTC  
> Url: https://github.com/boostorg/graph/pull/45#issuecomment-239383466  

https://svn.boost.org/trac/boost/ticket/11714 has a unit test for this. Shouldn't that be part of this pull request too?

---

## Comment 3

> Username: Belcourt  
> Created_at: 2016-11-01 02:07:43 UTC  
> Url: https://github.com/boostorg/graph/pull/45#issuecomment-257473085  

Any chance you could squash these and add the referenced unit test case?

---

## Comment 4

> Username: j-4  
> Created_at: 2016-11-02 10:06:28 UTC  
> Url: https://github.com/boostorg/graph/pull/45#issuecomment-257822249  

Hi,  
I added the unit test into the testing framework and squashed the commits.  
However, I have no idea to change the commit history here, so I generated a new pull request:  
https://github.com/boostorg/graph/pull/80  
  
Thanks for merging the bugfix :)  
Let me know, if you need anything else!

---
