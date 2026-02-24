# #19 Added graph property support to read_graphml [Merged]

> Username: josefcibulka  
> Created at: 2014-09-29 13:38:00 UTC  
> Updated at: 2014-11-03 07:21:08 UTC  
> Merged at: 2014-11-02 22:23:14 UTC  
> Closed at: 2014-11-02 22:23:14 UTC  
> Url: https://github.com/boostorg/graph/pull/19  

Graph properties from graphml files were never read. After this fix, the graphml reader uses the graph properties of the first top-level graph. The graph properties of the other top-level and all nested graphs are ignored.  
I also added testing of reading and writing of graph properties to graphml_test.cpp (and graphml_test.xml). The other changes in graphml_test.cpp are: changing assert to BOOST_CHECK and tolerance to small rounding errors while comparing properties of type double.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-11-02 20:51:21 UTC  
> Url: https://github.com/boostorg/graph/pull/19#issuecomment-61423000  

This patch looks great except that you've introduced a dependency on Boost.Test which we can't accept.  If you can close this PR and please resubmit the PR with all these changes except for those that introduce dependencies on Boost.Test, I'll commit it.  Nice job all around job.

---
