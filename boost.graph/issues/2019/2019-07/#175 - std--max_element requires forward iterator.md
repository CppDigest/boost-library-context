# #175 - std::max_element requires forward iterator [Open]

> Username: rogeeff  
> Created at: 2019-07-12 14:19:38 UTC  
> Updated at: 2019-10-16 22:10:36 UTC  
> Url: https://github.com/boostorg/graph/issues/175  

std::max_element contract required forward iterator. Graph library's uses this interface in betweenness_centrality_clustering on edge iterators, which according to boost iterator library are deduced as input iterators. This fails to build with libc++, which validates the category at compile time.   
  
The easiest solution is to handcraft the for loop:  
  
    auto it = edges_iters.first;  
    auto it_max = it;  
    while (++it != edges_iters.second) {  
      if (cmp(*it_max, *it)) it_max = it;  
    }  
    // Here we assume graph is not empty and contains at least one edge.  
    edge_descriptor e = *it_max;

---

## Comment 1

> Username: anadon  
> Created at: 2019-07-13 15:02:05 UTC  
> Url: https://github.com/boostorg/graph/issues/175#issuecomment-511128657  

Hello,  
  
I'm getting back to this repo after a month and a half.  While it is possible to make a loop in user code, that might not be the right option.  Though it may also be correct since a forward iterator may not make sense in a graph since it isn't convertible in a reliable way to a 1D directional structure.  I am working on Travis CI stuff right now and have a backlog, but I'll address this when I can.  If you would like a faster resolution and feel comfortable doing so, I will welcome you to create a test case and a code fix in a PR.

---

## Comment 2

> Username: morinmorin  
> Created at: 2019-09-21 08:31:11 UTC  
> Url: https://github.com/boostorg/graph/issues/175#issuecomment-533779790  

C++20's new overloads of `std::max_element` would work. But, no need to wait for C++20! We already have `boost::range::max_element` (or `boost::first_max_element` in Boost.Algorithm) that works with ForwardTraversal ranges (iterators).

---

## Comment 3

> Username: bkpoon  
> Created at: 2019-10-07 22:42:03 UTC  
> Url: https://github.com/boostorg/graph/issues/175#issuecomment-539235011  

I ran into this issue while trying to compile our code using Boost.Graph on macOS with Xcode 11. It looks like Boost.Range did what @morinmorin suggested by replacing `std::max_element` with `boost::first_max_element` (https://github.com/boostorg/range/commit/adcb071dc6b06e69aa4ddcc080d23695a5da378f).  
  
For testing our code, I modified `include/boost/graph/bc_clustering.hpp` to add the `<boost/algorithm/minmax_element.hpp>` header, removed the `<algorithm>` header, and replaced line 136  
  
            = *max_element(edges_iters.first, edges_iters.second, cmp);  
  
with  
  
            = *boost::first_max_element(edges_iters.first, edges_iters.second, cmp);  
  
I'm still testing this on the other platforms and compilers that we support, though. I can submit a pull request if that helps.

---

## Comment 4

> Username: bkpoon  
> Created at: 2019-10-16 22:09:59 UTC  
> Updated at: 2019-10-16 22:10:36 UTC  
> Url: https://github.com/boostorg/graph/issues/175#issuecomment-542912471  

I made a pull request (#190). There was another file, `include/boost/graph/howard_cycle_ratio.hpp`, that has the same issue, so I patched that as well.
