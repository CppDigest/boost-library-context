# #121 Fix the copy constructor of the subgraph. [Merged]

> Username: kenkangxgwe  
> Created at: 2018-10-17 00:56:46 UTC  
> Updated at: 2018-11-01 18:07:45 UTC  
> Merged at: 2018-11-01 18:07:45 UTC  
> Closed at: 2018-11-01 18:07:45 UTC  
> Url: https://github.com/boostorg/graph/pull/121  

In the previous version, the children and their properties are not copied correctly.

---

## Comment 1

> Username: anadon  
> Created_at: 2018-10-17 01:01:53 UTC  
> Url: https://github.com/boostorg/graph/pull/121#issuecomment-430452789  

We recently had a PR, #80, which touched on something close to this you might want to look at.  Before this gets accepted, please add a test case which illustrates the problem, doesn't work on the past incorrect behavior, and works with your PR.

---

## Comment 2

> Username: kenkangxgwe  
> Created_at: 2018-10-17 03:29:44 UTC  
> Url: https://github.com/boostorg/graph/pull/121#issuecomment-430477175  

I did not see the #80's relation with the copy ctor. Current copy ctor does not recursively copy the children and properties. I have added a test case in `test/subgraph.cpp`, you may check it.

---

## Comment 3

> Username: anadon  
> Created_at: 2018-10-17 03:32:23 UTC  
> Url: https://github.com/boostorg/graph/pull/121#issuecomment-430477575  

It was a quick thought.  I've been a little preoccupied.

---

## Review 4 [Commented]

> Username: anadon  
> Created_at: 2018-10-18 01:41:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/121#pullrequestreview-165898106  

📁 include/boost/graph/subgraph.hpp

```diff
 139 |-          m_graph = x.m_graph;
 138 |+             m_graph = x.m_graph;
 139 |+             m_edge_counter = x.m_edge_counter;
```

> Username: anadon  
> Created_at: 2018-10-18 01:41:35 UTC  
> Updated_at: 2018-10-18 07:06:41 UTC  
> Url: https://github.com/boostorg/graph/pull/121#discussion_r226146036  

If the subgraph is actually the entire graph keep count of its number of edges, else don't keep track of the member graph's number of edges?  This seems like a change in function.

> Username: kenkangxgwe  
> Created_at: 2018-10-18 01:47:35 UTC  
> Updated_at: 2018-10-18 07:06:41 UTC  
> Url: https://github.com/boostorg/graph/pull/121#discussion_r226146746  

If the `m_graph` is a child of its parent, we manually add all the vertices from `x.m_graph` to `m_graph`. This will automatically increase the `m_edge_counter` instead of assign `x.m_edge_counter` to it.


---

## Review 5 [Commented]

> Username: anadon  
> Created_at: 2018-10-18 01:42:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/121#pullrequestreview-165898273  

📁 test/subgraph.cpp

```diff
  20 | 
  21 |+ 
  22 |+ // This is a helper function to recusively compare two subgraphs.
```

> Username: anadon  
> Created_at: 2018-10-18 01:42:49 UTC  
> Updated_at: 2018-10-18 07:06:41 UTC  
> Url: https://github.com/boostorg/graph/pull/121#discussion_r226146194  

Please add more detail about what it is testing, and the motivation for testing it.


---

## Review 6 [Commented]

> Username: anadon  
> Created_at: 2018-10-18 01:43:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/121#pullrequestreview-165898377  

📁 test/subgraph.cpp

```diff
 135 |+     // and puts the first and last four of the vertices into two children.
 136 |+     // Do these again to the children, so there are 4 grandchildren with 2 vertices for each.
 137 |+     // Use the copy constructor to generate a copy and compare with the original one.
```

> Username: anadon  
> Created_at: 2018-10-18 01:43:37 UTC  
> Updated_at: 2018-10-18 07:06:41 UTC  
> Url: https://github.com/boostorg/graph/pull/121#discussion_r226146289  

What about subgraph edges?  Those also need testing.

> Username: kenkangxgwe  
> Created_at: 2018-10-18 01:46:45 UTC  
> Updated_at: 2018-10-18 07:06:41 UTC  
> Url: https://github.com/boostorg/graph/pull/121#discussion_r226146625  

I only checked the numbers. How do I compare two edge descriptors from two different graphs?

> Username: anadon  
> Created_at: 2018-10-18 01:52:50 UTC  
> Updated_at: 2018-10-18 07:06:41 UTC  
> Url: https://github.com/boostorg/graph/pull/121#discussion_r226147364  

You could load the structurally identical graphs with each node holding a unique ID in that graph identical to the equivalent edge in the other graph.

> Username: kenkangxgwe  
> Created_at: 2018-10-18 02:12:08 UTC  
> Updated_at: 2018-10-18 07:06:41 UTC  
> Url: https://github.com/boostorg/graph/pull/121#discussion_r226149623  

Ok, I just added a comparison of any two corresponding edge indices.


---

## Comment 7

> Username: anadon  
> Created_at: 2018-10-18 21:59:02 UTC  
> Url: https://github.com/boostorg/graph/pull/121#issuecomment-431178394  

I'm going to try to revisit this Wednesday.  I'm overloaded with work right now and can't give a review due diligence.

---

## Comment 8

> Username: kenkangxgwe  
> Created_at: 2018-10-19 01:37:47 UTC  
> Url: https://github.com/boostorg/graph/pull/121#issuecomment-431217196  

Take your time and have a nice weekend

---

## Comment 9

> Username: anadon  
> Created_at: 2018-11-01 14:00:04 UTC  
> Url: https://github.com/boostorg/graph/pull/121#issuecomment-435049293  

@jzmaddock This PR looks good to me.  It timed out on one test which could be rerun.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2018-11-01 18:07:39 UTC  
> Url: https://github.com/boostorg/graph/pull/121#issuecomment-435132133  

I've pushed a fix for the timeout to develop .travis.yml - I suspect the PR will just timeout again if we re-run, so I'll go ahead and merge and we'll see.

---
