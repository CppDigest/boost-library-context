# #6 Support for edge multiplicities in brandes_betweenness_centrality [Open]

> Username: javidcf  
> Created at: 2014-04-25 09:01:49 UTC  
> Updated at: 2018-10-16 17:16:06 UTC  
> Url: https://github.com/boostorg/graph/pull/6  

[Ticket #9935](https://svn.boost.org/trac/boost/ticket/9935)  
Implemented support for edges multiplicities in betweenness calculation. As explained in the ticket, the algorithm variation is taken from Ulrik Brandes' paper [On variants of shortest-path betweenness centrality and their generic computation](http://www.inf.uni-konstanz.de/algo/publications/b-vspbc-08.pdf), section 3.8, algorithm 11. Please note that this paper has a mistake on this algorithm, as explained in [Brandes' publications web page](http://www.inf.uni-konstanz.de/~brandes/publications/) - this patch implements the corrected algorithm.  
Note this implementation uses `static_property_map` and `make_static_property_map`, which are currently in the develop branch of the property_map library.  
  
**Note:** This PR cause compilation error when using `non_distributed_betweenness` centrality from graph-parallel. [PR #2 in that library](https://github.com/boostorg/graph_parallel/pull/2) fix these errors, while adding the multiplicity feature to that version of the algorithm.

---

## Comment 1

> Username: javidcf  
> Created_at: 2014-04-25 09:04:30 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-41372616  

If this pull request gets accepted, I can also try to implement this feature for the graph_parallel library.

---

## Comment 2

> Username: Belcourt  
> Created_at: 2014-05-18 21:32:29 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-43452658  

I pulled this code into develop and attempted to build it, graph_parallel fails to compile on Darwin with gcc, here's the error.  
  
In file included from ../libs/graph_parallel/test/distributed_csr_algorithm_test.cpp:30:  
../boost/graph/distributed/betweenness_centrality.hpp:1453:18: error: use of class template 'detail::graph::brandes_unweighted_shortest_paths' requires template arguments  
  detail::graph::brandes_unweighted_shortest_paths shortest_paths;  
                 ^  
../boost/graph/betweenness_centrality.hpp:171:10: note: template is declared here  
  struct brandes_unweighted_shortest_paths  
         ^  
In file included from ../libs/graph_parallel/test/distributed_csr_algorithm_test.cpp:30:  
../boost/graph/distributed/betweenness_centrality.hpp:1481:18: error: too few template arguments for class template 'brandes_dijkstra_shortest_paths'  
  detail::graph::brandes_dijkstra_shortest_paths<WeightMap> shortest_paths(weight_map);  
                 ^  
../boost/graph/betweenness_centrality.hpp:134:10: note: template is declared here  
  struct brandes_dijkstra_shortest_paths  
         ^  
1 warning and 2 errors generated.  
  
```  
"g++"  -ftemplate-depth-128 -O0 -fno-inline -Wall -g -dynamic -gdwarf-2 -fexceptions -fPIC  -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1  -I".." -I"/Users/kbelco/Projects/openmpi-1.4.5/gcc-4.2.1/include" -c -o "../bin.v2/libs/graph_parallel/test/distributed_csr_algorithm_test-1.test/darwin-4.2.1/debug/distributed_csr_algorithm_test.o" "../libs/graph_parallel/test/distributed_csr_algorithm_test.cpp"  
```  
  
Could you take a look at this and, ideally, submit a PR against graph_parallel as well?  I can help with the parallel (MPI) part.  
  
sanj2010-253-180:status kbelco$ gcc -v  
Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/usr/include/c++/4.2.1  
Apple LLVM version 5.1 (clang-503.0.40) (based on LLVM 3.4svn)  
Target: x86_64-apple-darwin12.5.0  
Thread model: posix

---

## Comment 3

> Username: Belcourt  
> Created_at: 2014-05-19 01:43:09 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-43460277  

I noticed that there's no test case for this feature, could you put together a test case and a little documentation for this as well?

---

## Comment 4

> Username: javidcf  
> Created_at: 2014-05-19 08:48:15 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-43478668  

@Belcourt Thanks for your comments. I haven't really used graph_parallel, and I wasn't aware that it used internal structs of graph. I'll look at it and try to make a fix so everything works together.  
  
Also, you're right, I should post a test case for this. To be honest, I wasn't completely sure there would be any interest in merging it. Anyway, the HTML documentation was updated to include the multiplicity map parameter.

---

## Comment 5

> Username: javidcf  
> Created_at: 2014-05-19 10:23:33 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-43486273  

@Belcourt I've added a quick fix for the compilation issue in [the develop branch of my fork of graph-parallel](https://github.com/javidcf/graph_parallel/tree/develop). I have not posted a pull request as I would like to actually introduce the multiplicity feature, but I'll do if I can't work it out.

---

## Comment 6

> Username: javidcf  
> Created_at: 2014-05-23 10:20:58 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-43992578  

@Belcourt I've put [a PR in graph-parallel](https://github.com/boostorg/graph_parallel/pull/2) to fix the errors you reported and to add the edge multiplicities to `non_distributed_betweenness_centrality`.

---

## Comment 7

> Username: Belcourt  
> Created_at: 2014-07-12 22:56:58 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-48826333  

In doc/betweenness_centrality.html, the MultiplicityMap parameter is missing from the template parameter list.  Further down, would you fix the several places where:  
  
negative_edge exception is one of the edges  
  
should be if, not is.  Also, since MultiplicityMap checks for non-postive, perhaps a different exception rather than using negative_edge, e.g. nonpostive_edge or something (also change in header)?  
  
For the brandes_dijkstra_visitor, I see you've changed the order of the arguments, could you add your MultiplicityMap to the end of the template, function, and members list?  Why change the argument order?  
  
Same question for brandes_betweenness_centrality_dispatch2, why put the MultiplicityMap ahead of the VertexIndexMap?  
  
Sorry I'm so late getting back to this.

---

## Comment 8

> Username: javidcf  
> Created_at: 2014-07-14 09:50:28 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-48882196  

@Belcourt Thank you for the feedback. I'll fix the doc right now.  
  
I wasn't sure about creating a new exception for nonpositive, but if you think it's okay I'll do it. I was thinking about making it a superclass of negative_edge (as it is a supercase of it), let me know if you do not agree.  
  
About the parameters order, I was trying to keep the weight and the multiplicity "near", because they seemed kind of "similar" parameters, both carrying actual edge information, while incoming, distance, path_count and vertex_index seemed more like auxiliary or output information. However, I have no problem in putting it in the end.

---

## Comment 9

> Username: anadon  
> Created_at: 2018-08-31 18:37:11 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-417754567  

I'm helping out with the PR backlog. Looks like you have a feature additions.  Given the age of this PR, I'd like you to chime in @javidcf and see where things stand.  This is to let you know and help me prioritize PR's.

---

## Comment 10

> Username: javidcf  
> Created_at: 2018-09-10 15:49:57 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-419962428  

@anadon Hey, sorry I missed this. This is an old one indeed, I implemented it because I needed the feature for a particular thing I did four years ago and it was missing, it worked but tbh I don't remember much of it. It seems I did make the changes Belcourt asked (order of parameters in a function and the documentation), and graph_parallel should also work with the corresponding changes in the referenced PR. What I think is missing is the testing, I think I had a look at it and didn't find it obvious how to do it, so I thought I would do it later at some point but never got around to it. Shameful of me :/

---

## Comment 11

> Username: anadon  
> Created_at: 2018-09-10 15:53:39 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-419963749  

@javidcf Should this PR be closed, then?

---

## Comment 12

> Username: javidcf  
> Created_at: 2018-09-10 16:09:21 UTC  
> Updated_at: 2018-09-10 16:09:52 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-419969194  

@anadon Honestly I don't really have time much now to relearn all my way through boost.graph, verify everything is fine, add the test case or whatever bits are missing and merge it with the current version. The feature is legit but I suppose maybe not super-demanded, so if there is no particular interest on it you can close it (tbh I didn't even think it was still open). Also if it is actually needed at some point is not even that hard to implement, I mean most of the changes are scaffolding, the actual logic is rather simple I think. Btw if this PR is closed the corresponding one in graph_parallel should be closed as well then I suppose.

---

## Comment 13

> Username: anadon  
> Created_at: 2018-10-15 19:33:26 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-429984428  

@jzmaddock This should probably be closed unless someone has a similar need in future.  It is a judgement call though, and Boost proper may want to keep this kind of PR around.  I'm not sure.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2018-10-16 17:16:06 UTC  
> Url: https://github.com/boostorg/graph/pull/6#issuecomment-430321333  

I think I'm not qualified to judge - for now I would be inclined to leave open and see if anyone else adds a "me too, please implement this!".  However, given that we have merge conflicts, no tests, and are blocked waiting for the graph_parallel PR to be merged this one requires a fair bit of work that's probbaly beyond the CMT IMO.

---
