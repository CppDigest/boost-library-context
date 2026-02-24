# #79 Fix dead links [Merged]

> Username: e-kwsm  
> Created at: 2016-11-02 07:30:46 UTC  
> Updated at: 2016-11-19 01:11:24 UTC  
> Merged at: 2016-11-02 14:51:15 UTC  
> Closed at: 2016-11-02 14:51:15 UTC  
> Url: https://github.com/boostorg/graph/pull/79  

As far as I can fix.

---

## Comment 1

> Username: e-kwsm  
> Created_at: 2016-11-02 07:41:00 UTC  
> Url: https://github.com/boostorg/graph/pull/79#issuecomment-257793462  

There are many dead links that I couldn't fix, but this repository seems to have no "issue" tab...

---

## Review 2 [Approved]

> Username: maelvls  
> Created_at: 2016-11-02 10:44:12 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/graph/pull/79#pullrequestreview-6787079  

I approve these changes, at least concerning `README.md`! (I am no maintainer!)

📁 README.md

```diff
  15 | 
  16 |- There is no mailing-list specific to Boost Graph, although you can use the general-purpose Boost [mailing-list](https://groups.google.com/forum/#!forum/boost-devel-archive) using the tag [graph].
  16 |+ There is no mailing-list specific to Boost Graph, although you can use the general-purpose Boost [mailing-list](http://lists.boost.org/mailman/listinfo.cgi/boost-users) using the tag [graph].
```

> Username: maelvls  
> Created_at: 2016-11-02 10:42:36 UTC  
> Updated_at: 2016-11-02 10:44:12 UTC  
> Url: https://github.com/boostorg/graph/pull/79#discussion_r86113320  

Weird, we wrote this README not so long ago 😕   
And you are right: the google group boost-devel-archive seems to have disappeared! (although being still in google results)


---

## Comment 3

> Username: e-kwsm  
> Created_at: 2016-11-02 21:39:05 UTC  
> Url: https://github.com/boostorg/graph/pull/79#issuecomment-258007222  

Should I report other dead links in this PR instead of creating new issues?

---

## Comment 4

> Username: Belcourt  
> Created_at: 2016-11-02 21:41:35 UTC  
> Url: https://github.com/boostorg/graph/pull/79#issuecomment-258007787  

Sure, report them all here.  Perhaps you can squash all the commits so it's easier to review.

---

## Comment 5

> Username: e-kwsm  
> Created_at: 2016-11-02 22:00:35 UTC  
> Url: https://github.com/boostorg/graph/pull/79#issuecomment-258012820  

The link to the BGL book (http://www.awprofessional.com/title/0201729148) is dead and the correct one seem http://www.informit.com/store/boost-graph-library-user-guide-and-reference-manual-9780201729146 (I'm not sure though).  
  
The following links are dead:  
- [ ] http://bioinformatics.icmb.utexas.edu/lgl/  
- [ ] http://hyperworx.org  
- [ ] http://lists.boost.org/MailArchives/boost/msg48752.php  
- [ ] http://map1.squeakfoundation.org/sm/package/5729d80a-822b-4bc2-9420-ef7ecaea8553  
- [ ] http://people.freenet.de/andreas.scherer  
- [ ] http://photon.poly.edu/~hbr/cs903/  
- [ ] http://siek.info/bgl.html  
- [ ] http://toc.lcs.mit.edu/~clr/  
- [ ] http://www.acm.org/pubs/citations/proceedings/oops/320384/p399-siek/  
- [ ] http://www.avglab.com/andrew/CATS/maxflow_formats.htm  
- [ ] http://www.bioconductor.org/repository/devel/vignette/RBGL.pdf  
- [ ] http://www.cs.panam.edu/~rfowler/papers/1994_kumar_fowler_A_Spring_UTPACSTR.pdf  
- [ ] http://www.cs.rpi.edu/~gregod/boost/tools/doc/revision  
- [ ] http://www.cuj.com/documents/s=8470/cuj0307tan/  
- [ ] http://www.ddj.com/articles/2000/0009/0009toc.htm  
- [ ] http://www.geom.umn.edu/locate/qhull  
- [ ] http://www.geom.umn.edu/~bradb/qhull-news.html#use  
- [ ] http://www.graphviz.org/cvs/doc/info/lang.html  
- [ ] http://www.lsc.nd.edu  
- [ ] http://www.lsc.nd.edu/downloads/research/ggcl/papers/thesis.pdf  
- [ ] http://www.lsc.nd.edu/downloads/research/ggcl/papers/thesis.ps  
- [ ] http://www.lsc.nd.edu/research/mtl  
- [ ] http://www.lsi.upc.edu/~dmitry  
- [ ] http://www.mpi-sb.mpg.de/LEDA/MANUAL/bgraph.html  
- [ ] http://www.oakland.edu/~grossman/erdoshp.html  
- [ ] http://www.oonumerics.org/tmpw00/  
- [ ] http://www.osl.iu.edu/~dgregor/bgl-python/  
- [ ] http://www.osl.iu.edu/~lums

---

## Comment 6

> Username: e-kwsm  
> Created_at: 2016-11-02 22:03:28 UTC  
> Url: https://github.com/boostorg/graph/pull/79#issuecomment-258013484  

I couldn't check the following links since login is required:  
- [ ] http://citeseer.ist.psu.edu/alberts98software.html  
- [ ] http://citeseer.ist.psu.edu/eppstein99dynamic.html

---

## Comment 7

> Username: e-kwsm  
> Created_at: 2016-11-02 22:18:16 UTC  
> Url: https://github.com/boostorg/graph/pull/79#issuecomment-258016664  

Another problem: the following examples, which have other extensions than `.cpp`, in http://www.boost.org/doc/libs/1_62_0/libs/graph/example/ are dead.  
This repository actually has them, though.  
- [ ] [graph/example/adjacency_list.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/adjacency_list.expected)  
- [ ] [graph/example/bcsstk01](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/bcsstk01)  
- [ ] [graph/example/bcsstk01.rsa](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/bcsstk01.rsa)  
- [ ] [graph/example/bellman_ford.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/bellman_ford.expected)  
- [ ] [graph/example/bfs.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/bfs.expected)  
- [ ] [graph/example/bfs_basics.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/bfs_basics.expected)  
- [ ] [graph/example/boost_web.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/boost_web.dat)  
- [ ] [graph/example/boost_web_graph.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/boost_web_graph.expected)  
- [ ] [graph/example/comm_network.graph](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/comm_network.graph)  
- [ ] [graph/example/components_on_edgelist.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/components_on_edgelist.expected)  
- [ ] [graph/example/concept_checks.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/concept_checks.expected)  
- [ ] [graph/example/connected_components.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/connected_components.expected)  
- [ ] [graph/example/container_gen.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/container_gen.expected)  
- [ ] [graph/example/cuthill_mckee_ordering.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/cuthill_mckee_ordering.expected)  
- [ ] [graph/example/dave.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/dave.expected)  
- [ ] [graph/example/dfs.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/dfs.expected)  
- [ ] [graph/example/dfs_basics.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/dfs_basics.expected)  
- [ ] [graph/example/dfs_parenthesis.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/dfs_parenthesis.expected)  
- [ ] [graph/example/dijkstra.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/dijkstra.expected)  
- [ ] [graph/example/edge_basics.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/edge_basics.expected)  
- [ ] [graph/example/edge_iterator_constructor.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/edge_iterator_constructor.dat)  
- [ ] [graph/example/edge_property.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/edge_property.expected)  
- [ ] [graph/example/exterior_property_map.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/exterior_property_map.expected)  
- [ ] [graph/example/family_tree.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/family_tree.expected)  
- [ ] [graph/example/fibonacci_heap.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/fibonacci_heap.expected)  
- [ ] [graph/example/figs/cc-internet.dot](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/figs/cc-internet.dot)  
- [ ] [graph/example/figs/dfs-example.dot](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/figs/dfs-example.dot)  
- [ ] [graph/example/figs/edge-connectivity.dot](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/figs/edge-connectivity.dot)  
- [ ] [graph/example/figs/ospf-graph.dot](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/figs/ospf-graph.dot)  
- [ ] [graph/example/figs/scc.dot](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/figs/scc.dot)  
- [ ] [graph/example/figs/telephone-network.dot](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/figs/telephone-network.dot)  
- [ ] [graph/example/file_dependencies.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/file_dependencies.expected)  
- [ ] [graph/example/filtered_graph.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/filtered_graph.expected)  
- [ ] [graph/example/gerdemann.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/gerdemann.expected)  
- [ ] [graph/example/graphviz_example.dot](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/graphviz_example.dot)  
- [ ] [graph/example/graphviz_test.dot](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/graphviz_test.dot)  
- [ ] [graph/example/in_edges.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/in_edges.expected)  
- [ ] [graph/example/incremental_components.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/incremental_components.expected)  
- [ ] [graph/example/info_network.graph](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/info_network.graph)  
- [ ] [graph/example/interior_property_map.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/interior_property_map.expected)  
- [ ] [graph/example/johnson.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/johnson.expected)  
- [ ] [graph/example/kevin-bacon.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/kevin-bacon.dat)  
- [ ] [graph/example/kevin-bacon2.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/kevin-bacon2.dat)  
- [ ] [graph/example/kevin-bacon2.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/kevin-bacon2.expected)  
- [ ] [graph/example/kevin_bacon.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/kevin_bacon.expected)  
- [ ] [graph/example/kruskal.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/kruskal.expected)  
- [ ] [graph/example/leda-regression.cfg](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/leda-regression.cfg)  
- [ ] [graph/example/makefile-dependencies.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/makefile-dependencies.dat)  
- [ ] [graph/example/makefile-target-names.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/makefile-target-names.dat)  
- [ ] [graph/example/max_flow.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/max_flow.dat)  
- [ ] [graph/example/max_flow.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/max_flow.expected)  
- [ ] [graph/example/max_flow2.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/max_flow2.dat)  
- [ ] [graph/example/max_flow3.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/max_flow3.dat)  
- [ ] [graph/example/max_flow4.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/max_flow4.dat)  
- [ ] [graph/example/max_flow5.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/max_flow5.dat)  
- [ ] [graph/example/max_flow6.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/max_flow6.dat)  
- [ ] [graph/example/max_flow7.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/max_flow7.dat)  
- [ ] [graph/example/max_flow8.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/max_flow8.dat)  
- [ ] [graph/example/max_flow9.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/max_flow9.dat)  
- [ ] [graph/example/miles_span.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/miles_span.expected)  
- [ ] [graph/example/modify_graph.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/modify_graph.expected)  
- [ ] [graph/example/ordered_out_edges.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/ordered_out_edges.expected)  
- [ ] [graph/example/prim.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/prim.expected)  
- [ ] [graph/example/prism_3_2.graph](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/prism_3_2.graph)  
- [ ] [graph/example/prob_network.graph](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/prob_network.graph)  
- [ ] [graph/example/quick_tour.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/quick_tour.expected)  
- [ ] [graph/example/regression.cfg](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/regression.cfg)  
- [ ] [graph/example/remove_edge_if_bidir.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/remove_edge_if_bidir.expected)  
- [ ] [graph/example/remove_edge_if_dir.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/remove_edge_if_dir.expected)  
- [ ] [graph/example/remove_edge_if_undir.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/remove_edge_if_undir.expected)  
- [ ] [graph/example/reverse_graph.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/reverse_graph.expected)  
- [ ] [graph/example/scc.dot](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/scc.dot)  
- [ ] [graph/example/sgb-regression.cfg](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/sgb-regression.cfg)  
- [ ] [graph/example/social_network.graph](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/social_network.graph)  
- [ ] [graph/example/strong_components.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/strong_components.expected)  
- [ ] [graph/example/subgraph.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/subgraph.expected)  
- [ ] [graph/example/target-compile-costs.dat](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/target-compile-costs.dat)  
- [ ] [graph/example/tc.dot](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/tc.dot)  
- [ ] [graph/example/test-astar-cities.dot](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/test-astar-cities.dot)  
- [ ] [graph/example/topo_sort.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/topo_sort.expected)  
- [ ] [graph/example/undirected_adjacency_list.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/undirected_adjacency_list.expected)  
- [ ] [graph/example/vector_as_graph.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/vector_as_graph.expected)  
- [ ] [graph/example/vertex_basics.expected](http://www.boost.org/doc/libs/1_62_0/libs/graph/example/vertex_basics.expected)

---

## Comment 8

> Username: e-kwsm  
> Created_at: 2016-11-05 17:08:40 UTC  
> Url: https://github.com/boostorg/graph/pull/79#issuecomment-258625072  

This comment is not about dead links.  
  
---  
  
Examples in http://www.boost.org/doc/libs/1_62_0/libs/graph/example/ are not sorted, which makes so hard to find one; they should be sorted.  
  
Additionally there are some files whose names are confusing and whose contents are likely similar:  
- `bellman-example.cpp` (actually an example for `bellman_ford_shortest_paths`),  
  `bellman-ford-internet.cpp`, and `bellman_ford.expected`  
- `connected-components.cpp` and `connected_components.cpp`  
- `family-tree-eg.cpp` and `family_tree.expected`  
- `incremental-components-eg.cpp`, `incremental_components.cpp`, and `incremental_components.expected`  
- `interior_pmap_bundled.cpp` and `interior_property_map.cpp`  
- `knights-tour.cpp` and `knights_tour.expected`  
- `kruskal-example.cpp`, `kruskal-telephone.cpp`, and `kruskal.expected`  
- `prim-example.cpp`, `prim-telephone.cpp` and `prim.expected`  
- `quick-tour.cpp`, `quick_tour.cpp`, and `quick_tour.expected`  
- `reverse-graph-eg.cpp` and `reverse_graph.expected`  
- `strong-components.cpp`, `strong_components.cpp`, and `strong_components.expected`  
- `vector-as-graph.cpp` and `vector_as_graph.expected`  
  
An expected output of `foo.cpp` should be `foo.expected`.

---

## Comment 9

> Username: Belcourt  
> Created_at: 2016-11-05 18:25:58 UTC  
> Url: https://github.com/boostorg/graph/pull/79#issuecomment-258631511  

Okay, I'll work on cleaning these up, thanks for reporting.

---

## Comment 10

> Username: murraycu  
> Created_at: 2016-11-05 23:40:10 UTC  
> Url: https://github.com/boostorg/graph/pull/79#issuecomment-258649791  

This kind of confusion is why I wanted to move many of these files into an examples/book sub-directory:  
https://github.com/boostorg/graph/pull/64/commits/06eebd9ea4f924b332d9354f5be9fcac5eef8599  
The book example files were just dumped there in 2001 even though they had very similar names to existing files, but not necessarily the same example code: https://github.com/boostorg/graph/commit/5215e9b4f201bf4509a78abe89d16cb8f4199bbd  
  
Maybe we should do that before trying to guess at what files the documentation should really link to.

---
