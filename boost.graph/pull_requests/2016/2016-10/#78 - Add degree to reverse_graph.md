# #78 Add degree to reverse_graph [Merged]

> Username: mdb256  
> Created at: 2016-10-27 03:23:38 UTC  
> Updated at: 2018-05-23 13:34:19 UTC  
> Merged at: 2016-10-31 20:03:54 UTC  
> Closed at: 2016-10-31 20:03:54 UTC  
> Url: https://github.com/boostorg/graph/pull/78  

degree is required for BidirectionalGraphConcept.  
Also adds the concept check to the reverse_graph unit test.

---

## Comment 1

> Username: ilovezfs  
> Created_at: 2016-10-31 16:35:05 UTC  
> Url: https://github.com/boostorg/graph/pull/78#issuecomment-257345193  

As I mentioned on [#29](https://github.com/boostorg/graph/pull/29#issuecomment-257316324), I'm aware of the following cases where the bug that this PR fixes has caused trouble, but there are likely many more:  
https://github.com/01org/hyperscan/issues/39  
https://github.com/bcgsc/abyss/issues/130  
https://git.skewed.de/count0/graph-tool/issues/347

---

## Comment 2

> Username: ilovezfs  
> Created_at: 2016-10-31 17:20:44 UTC  
> Url: https://github.com/boostorg/graph/pull/78#issuecomment-257358354  

This patch (at least as adapted here https://raw.githubusercontent.com/ilovezfs/formula-patches/207b1060aa08bdbc2ada0cd7769f0934b5b521ea/boost/boost162-add-degree-to-reverse_graph.patch for the tarball) does not seem to work for graph-tool's build failure.  
  
I'm still seeing  
  
```  
/usr/local/include/boost/graph/graph_concepts.hpp:131:13: error: no matching function for call to 'degree'  
        n = degree(v, g);  
```  
  
Log here:  
https://gist.github.com/ilovezfs/4bf6e521624d4757ed236e9d7c8522a3

---

## Comment 3

> Username: ilovezfs  
> Created_at: 2016-11-01 07:04:26 UTC  
> Url: https://github.com/boostorg/graph/pull/78#issuecomment-257501984  

This PR is fine. The problem was that graph-tool has its own modified filtered_graph.hpp

---

## Comment 4

> Username: tkoeppe  
> Created_at: 2018-05-05 21:46:35 UTC  
> Url: https://github.com/boostorg/graph/pull/78#issuecomment-386837340  

Is compressed_sparse_row_graph similarly affected? It looks like it's missing a `degree` function?!

---

## Comment 5

> Username: emmenlau  
> Created_at: 2018-05-23 13:34:19 UTC  
> Url: https://github.com/boostorg/graph/pull/78#issuecomment-391348363  

I also get a similar error by using graph_concepts.hpp:  
```  
/tmp/include/boost/graph/graph_concepts.hpp: In instantiation of ‘boost::concepts::BidirectionalGraph<G>::~BidirectionalGraph() [with G = vigra::GridGraph<3u, boost::undirected_tag>]’:  
/tmp/include/boost/graph/graph_concepts.hpp:114:5:   required from ‘static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::BidirectionalGraphConcept<vigra::GridGraph<3u, boost::undirected_tag> >]’  
/tmp/vigra/test/gridgraph/test.cxx:591:9:   required from ‘void GridGraphTests<N>::testBasics() [with DirectedTag = boost::undirected_tag; vigra::NeighborhoodType NType = (vigra::NeighborhoodType)1u; unsigned int N = 3u]’  
/tmp/vigra/test/gridgraph/test.cxx:1248:13:   required from ‘GridgraphTestSuiteN<N>::GridgraphTestSuiteN() [with unsigned int N = 3u]’  
/tmp/vigra/test/gridgraph/test.cxx:1292:13:   required from here  
/tmp/include/boost/graph/graph_concepts.hpp:131:19: error: ‘degree’ was not declared in this scope  
         n = degree(v, g);  
                   ^  
```  
Where is the best place to report this?

---
