# #29 Fix for implementation and documentation of BidirectionalGraphConcept. [Merged]

> Username: jakobandersen  
> Created at: 2015-01-02 22:21:52 UTC  
> Updated at: 2016-10-31 14:56:37 UTC  
> Merged at: 2016-04-23 18:27:51 UTC  
> Closed at: 2016-04-23 18:27:52 UTC  
> Url: https://github.com/boostorg/graph/pull/29  

- Add missing check for the 'degree' function in BidirectionalGraphConcept.  
- Fix the concept checking class in the documentation for BidirectionalGraphConcept. Was missing both 'degree' and 'in_degree'.  
- Implementation of missing 'degree' function for filtered_graph.  
- Add test for undirected graphs in filtered_graph.

---

## Comment 1

> Username: ilovezfs  
> Created_at: 2016-10-31 14:56:37 UTC  
> Url: https://github.com/boostorg/graph/pull/29#issuecomment-257316324  

This has caused a few regressions I'm aware of, and probably many more. As far as I can tell it wasn't even in the release notes for 1.62.0 so I'm not sure if those regressions are "intended."  
  
https://github.com/01org/hyperscan/issues/39  
https://github.com/bcgsc/abyss/issues/130  
https://git.skewed.de/count0/graph-tool/issues/347

---
