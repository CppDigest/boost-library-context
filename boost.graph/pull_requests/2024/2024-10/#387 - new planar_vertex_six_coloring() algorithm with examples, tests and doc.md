# #387 new planar_vertex_six_coloring() algorithm with examples, tests and doc [Open]

> Username: Hermann-SW  
> Created at: 2024-10-02 13:55:22 UTC  
> Updated at: 2026-01-06 12:52:41 UTC  
> Url: https://github.com/boostorg/graph/pull/387  

Why <kbd>planar_vertex_six_coloring()</kbd> is useful can be seen in the first of three tests for that algorithm.  
The existing <kbd>sequential_vertex_coloring()</kbd> can be forced to use $k$ colors for any $k$ (k=15 in test).  
Planar graphs can always be 4-colored, but algorithm for that has quadratic runtime.  
<kbd>planar_vertex_six_coloring()</kbd> has linear runtime.  
  
For that runtime a copy of the graph needs to be used, that allows for constant time edge removal.  
For that purpose new class <kbd>undirected_graph_constant_time_edge_add_and_remove</kbd> derived from <kbd>undirected_graph</kbd> was implemented.  
To do its job a new protected member function <kbd>remove_edge_()</kbd> was added to <kbd>undirected graph</kbd> that allows for O(1) edge removal if both <kbd>out_edge_iterator</kbd> of an edge are passed (which the new class does).  
  
The example for <kbd>undirected_graph_constant_time_edge_add_and_remove</kbd> does demonstrate quadratic runtime of <kbd>undirected graph</kbd> for a task needed for 6-coloring algorithm, while new class shows only linear time for that:  
```  
pi@raspberrypi5:~/graph/example $ g++ -O3 undirected_graph_constant_time_edge_add_and_remove.cpp   
pi@raspberrypi5:~/graph/example $ ./a.out   
Wn    ugraph_o1 ugraph     runtimes [s]  
2000  0.000292  0.00743  
4000  0.000917  0.04115  
8000  0.002566  0.275956  
16000  0.006217  1.69308  
32000  0.016996  19.8986  
pi@raspberrypi5:~/graph/example $   
```  
  
The <kbd>planar_vertex_six_coloring()</kbd> example creates a  random maximal planar graph with 1million edges, and 6-colors it in few seconds:  
```  
pi@raspberrypi5:~/graph/example $ g++ -O3 planar_vertex_six_coloring.cpp   
pi@raspberrypi5:~/graph/example $ ./a.out   
simple_maximal_planar_random_graph(g, argc < 2 ? 333335 : atoi(argv[1])); 0.505189s  
333335 vertices  
999999 egdes  
vertices_size_type num_colors = planar_vertex_six_coloring(g, color); 1.35317s  
6 colors  
vertex coloring verified  
pi@raspberrypi5:~/graph/example $   
```  
  
Finally the test of new algorithm does compare <kbd>sequential_vertex_coloring()</kbd> and <kbd>planar_vertex_six_coloring()</kbd> on   
1. crafted graph that forces sequential coloring to use 15 colors (on graph with 1000 vertices)  
2. new planar_input_graphs/pentakis_dodecahedron.leda  
3. new planar_input_graphs/maximal_planar_1000.leda  
  
Without arguments passed it is just the needed test, with arguments "15 output" it does the same as test does and generates output:  
```  
pi@raspberrypi5:~/graph/test $ g++ -O3 planar_vertex_six_coloring.cpp   
pi@raspberrypi5:~/graph/test $ ./a.out   
No errors detected.  
pi@raspberrypi5:~/graph/test $ ./a.out 15 output  
create(g, k); 0.000174  
vertices_size_type num_colors = sequential_vertex_coloring(g, color); 2e-05  
vertices_size_type num_color6 = planar_vertex_six_coloring(g, color); 0.000472  
987/1595 vertices/edges  
15/6 colors used  
read_leda_graph(g, "planar_input_graphs/pentakis_dodecahedron.leda"); 0.000285  
num_colors = sequential_vertex_coloring(g, color); 4e-06  
num_color6 = planar_vertex_six_coloring(g, color); 2.3e-05  
32/90 vertices/edges  
5/4 colors used  
read_leda_graph(g, "planar_input_graphs/maximal_planar_1000.leda"); 0.000683  
num_colors = sequential_vertex_coloring(g, color); 5.4e-05  
num_color6 = planar_vertex_six_coloring(g, color); 0.000738  
1000/2994 vertices/edges  
6/6 colors used  
No errors detected.  
pi@raspberrypi5:~/graph/test $   
```  
  
  
This is my first pull request for boost.  
So I tried to provide clean code, with examples and test.  
And with new documentation for the derived class and the new algorithm.  
I used existing doc for undirected_graph and sequential_vertex_coloring as basis for new doc.

---

## Comment 1

> Username: Hermann-SW  
> Created_at: 2024-10-07 11:59:56 UTC  
> Updated_at: 2024-10-07 14:47:09 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2396730445  

I need help.  
I tried to fix the issues in new code for the PR.  
Still CI is failing, but for code outside of my changes.  
With 1.86 Graph requires C++14.  
  
Who will resolve these issues outside of my PR?  
If I should do, what steps are needed by me?  
Should they be part of this PR?  
  
Examples from:  
https://drone.cpp.al/boostorg/graph/252/1/2  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/graph/test/gursoy_atun_layout_test.test/gcc-5/debug/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/gursoy_atun_layout_test.o  
In file included from ../../../boost/math/constants/constants.hpp:11:0,  
                 from ../../../boost/graph/topology.hpp:16,  
                 from ../../../boost/graph/gursoy_atun_layout.hpp:34,  
                 from gursoy_atun_layout_test.cpp:10:  
../../../boost/math/tools/config.hpp:28:6: warning: #warning "Boost.Math requires C++14" [-Wcpp]  
 #    warning "Boost.Math requires C++14"  
      ^  
In file included from ../../../boost/math/tools/mp.hpp:15:0,  
                 from ../../../boost/math/policies/policy.hpp:11,  
                 from ../../../boost/math/constants/constants.hpp:16,  
                 from ../../../boost/graph/topology.hpp:16,  
                 from ../../../boost/graph/gursoy_atun_layout.hpp:34,  
                 from gursoy_atun_layout_test.cpp:10:  
../../../boost/math/tools/type_traits.hpp:208:12: error: 'std::is_final' has not been declared  
 using std::is_final;  
 ```  
   
 ```  
 In file included from ../../../boost/math/tools/mp.hpp:15:0,  
                 from ../../../boost/math/policies/policy.hpp:11,  
                 from ../../../boost/math/constants/constants.hpp:16,  
                 from ../../../boost/graph/topology.hpp:16,  
                 from ../../../boost/graph/fruchterman_reingold.hpp:16,  
                 from layout_test.cpp:16:  
../../../boost/math/tools/type_traits.hpp:426:34: warning: variable templates only available with -std=c++14 or -std=gnu++14  
 BOOST_MATH_INLINE_CONSTEXPR bool is_nothrow_default_constructible_v = boost::math::is_nothrow_default_constructible<T>::value;  
                                  ^  
```  
  
The CI seems not to know that it should not test below C++14.  
Who can fix that?  
  
https://www.boost.org/users/history/version_1_86_0.html  
```  
...  
Graph:  
Major update: C++14 is the new minimum standard; this was partly dictated   
by dependencies (at least to C++11) and partly by choice. If you require   
support for an older standard, please contact the maintainer.  
...  
```

---

## Comment 2

> Username: grisumbras  
> Created_at: 2024-10-07 19:40:35 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2397741137  

C++11 CI jobs are launched due to lines like this: https://github.com/boostorg/graph/blob/develop/.drone.star#L17  
  
If you remove those lines, those jobs will disappear.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2024-10-07 23:41:23 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2398177807  

Thanks, and sorry, I'm a bit behind with updating the CI configuration.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2024-10-09 03:19:56 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2401189406  

I fixed `develop`, so please update your branch to resolve the conflicts.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2024-10-11 04:17:02 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2406523712  

Now, I forgot to say: thank you for a most interesting algorithm proposal. I'm not certain, but I imagine most algorithms and data structures in Boost.Graph are based on published literature, so although I am quite happy and even excited to accept novel algorithms and data structures, there is a higher burden of proof on the part of the contributor to demonstrate good theoretical design.  
  
It will take some time to evaluate, and if there are any issues found then it may require several rounds of review and evolution, but don't be despondent, that's how we (try to) maintain high quality.

---

## Comment 6

> Username: Hermann-SW  
> Created_at: 2024-10-11 06:17:48 UTC  
> Updated_at: 2024-10-11 06:59:16 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2406627799  

Sorry that I did not mention the literature, it is:  
  
Marek Chrobak, David Eppstein  
"Planar orientations with low out-degree and compaction of adjacency matrices"  
Theoretical Computer Science 1991  
https://dl.acm.org/doi/10.1016/0304-3975%2891%2990020-3  
  
Without paywall from author:  
https://core.ac.uk/download/pdf/82533794.pdf  
  
![image](https://github.com/user-attachments/assets/dafdd91b-b32f-4458-9107-06a0f4251f64)  
  
This code implements the determination of 5-bounded acyclic orientation of G, the order of vertices visited is in vector "visited":  
https://github.com/Hermann-SW/graph/blob/develop/include/boost/graph/planar_vertex_six_coloring.hpp#L80-L103  
  
Before   
https://github.com/Hermann-SW/graph/blob/develop/include/boost/graph/planar_vertex_six_coloring.hpp#L55-L77  
only creates a copy U of input graph G of new type <kbd>undirected_graph_with_constant_time_edge_add_and_remove</kbd> with linkage (diagram in source code) in both directions. Only graph U allows to determine the 5-bounded acyclic orientation of G in linear time (see quadratic runtime demo of <kbd>undirected_graph</kbd> for this task in initial comment of this PR).  
  
Finally   
https://github.com/Hermann-SW/graph/blob/develop/include/boost/graph/planar_vertex_six_coloring.hpp#L106-L124  
assigns the lowest possible color to vertex v, different to its at most 5 neighbors from the acyclic orientation. The vertices get colored in reverse order as visted while determining the 5-bounded acyclic orientation. This step guarantees to color the input graph with at most 6 colors.  
  
  
Now that you opened the discussion, the first two parts just determine the order for coloring the vertices with at most 6 colors. After having submitted I realized that the 3rd part can be replaced with a single call to <kbd>sequential_vertex_coloring()</kbd> with passing the order determined as 3rd argument:    
https://www.boost.org/doc/libs/1_86_0/libs/graph/doc/sequential_vertex_coloring.html    
If you agree, I can replace lines 106-124 with the call.  
  
And add a comment "determine 5-bounded acyclic orientation" with link to literature for 2nd part.    
And add a comment "determine copy U of G with linkage" to 1st part.

---

## Comment 7

> Username: Hermann-SW  
> Created_at: 2024-10-24 19:40:19 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2436203125  

I asked myself whether new class <kbd>undirected_graph_constant_time_edge_add_and_remove</kbd> is really needed, and it is. Reason is that from user code only iterator of a vertices out_edges() can be accessed, but not the out_edges list itself. That list would be needed for calling std::list [erase()](https://en.cppreference.com/w/cpp/container/list/erase) from user code.

---

## Comment 8

> Username: Hermann-SW  
> Created_at: 2024-11-08 18:43:43 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2465517974  

@jeremy-murphy   
There is an infrastructure problem making only Linux clang++ 9.17 eyerything fail, not my code according log.  
Can you help to get the CI run again?

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2024-11-09 04:28:16 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2466040860  

I've noticed a couple of odd things about the CI lately, I might have to ask someone with more intimate knowledge of it to have a look.

---

## Comment 10

> Username: jeremy-murphy  
> Created_at: 2024-11-09 04:29:37 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2466041304  

Btw I don't think I can re-run the CI on the same commit. I think it requires a new commit to trigger it, or possibly closing and reopening the PR might work.

---

## Comment 11

> Username: grisumbras  
> Created_at: 2024-11-09 08:48:47 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2466123037  

That's a transient error.

---

## Comment 12

> Username: grisumbras  
> Created_at: 2024-11-09 08:54:22 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2466132912  

@jeremy-murphy if you can't rerun Drone builds, ask @sdarwin to give you the necessary permissions.

---

## Comment 13

> Username: Hermann-SW  
> Created_at: 2024-11-09 10:29:37 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2466163451  

@jeremy-murphy @grisumbras   
Thanks for rerunning, again it is failing for clang++ 9.17 everything only.  
No indication that my code is responsible.  
Also with return code 0 and no error message.  
How to handle this now?

---

## Comment 14

> Username: grisumbras  
> Created_at: 2024-11-09 10:37:22 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2466165908  

I don't think there was a rerun. If you follow the Drone link, you can see that the build is from the 2nd of November.

---

## Comment 15

> Username: sdarwin  
> Created_at: 2024-11-09 12:35:01 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2466198326  

After re-running, the job succeeded.    
You can either click 'restart' from the upper-right corner in drone, or push a small change to the pull request.

---

## Comment 16

> Username: Hermann-SW  
> Created_at: 2024-11-09 17:08:30 UTC  
> Updated_at: 2024-11-09 18:49:24 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2466300894  

@grisumbras @jeremy-murphy   
This pull request consists of 14 new or changed files.  
Since I created the pull request 6 weeks ago I have learned much more on how to do things better with Boost graph.  
  
Before I do the changes proposed, I want to ask you whether I should do the code improvement or not.  
  
These 3 files are not needed anymore:  
- doc/undirected_graph_constant_time_edge_add_and_remove.html  
- example/undirected_graph_constant_time_edge_add_and_remove.cpp  
- include/boost/graph/undirected_graph_constant_time_edge_add_and_remove.hpp  
  
The changes in these two deep graph files can be reverted as they are not needed anymore:  
- include/boost/graph/detail/adjacency_list.hpp  
- include/boost/graph/undirected_graph.hpp  
  
The pull request would then consist of these 9 files only:  
- include/boost/graph/planar_vertex_six_coloring.hpp  
- example/planar_vertex_six_coloring.cpp  
- test/Jamfile.v2  
- test/planar_vertex_six_coloring.cpp  
- test/planar_input_graphs/pentakis_dodecahedron.leda  
- test/planar_input_graphs/maximal_planar_1000.leda  
- doc/table_of_contents.html  
- doc/planar_vertex_six_coloring.html  
- doc/figs/planar_vertex_coloring_with_5.png  
  
So how does the simpification work?  
New class o1_edge_visitor derived from "edge_index_update_visitor" allows for the needed O(1) remove_edge().  
I created a complete self-contained demo in this gist (with comments) for review:  
https://gist.github.com/Hermann-SW/539d2396e567a2ee26f4e0213f3b5fe7  
  
1)  
There is only one use of "g.impl()" in new visit_edge() that removes an edge in O(1)  
by moving both out_edge_iterators of the edge to the fist positions in the out_edge_lists.  
  
Use of "g.impl()" is essential, without no O(1) remove_edge is possible.  
Is that OK?  
  
2)  
https://en.cppreference.com/w/cpp/iterator/prev  
states:  
"Although the expression --c.end() often compiles, it is not guaranteed to do so"  
  
I tried to use "std::prev()" in line 49, but then the code just hangs indefinitely, so I cannot use it?  
  
  
Thanks in advance for your help.

---

## Comment 17

> Username: jeremy-murphy  
> Created_at: 2024-11-10 23:27:50 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2466985469  

> After re-running, the job succeeded. You can either click 'restart' from the upper-right corner in drone, or push a small change to the pull request.  
  
Thanks, Sam!

---

## Comment 18

> Username: jeremy-murphy  
> Created_at: 2024-11-10 23:29:48 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2466986071  

> @jeremy-murphy if you can't rerun Drone builds, ask @sdarwin to give you the necessary permissions.  
  
@sdarwin, is that possible? It would sure come in handy some times.

---

## Comment 19

> Username: sdarwin  
> Created_at: 2024-11-11 12:25:42 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-2468057197  

>  It would sure come in handy some times.  
  
Most drone permissions are based on github.    
  
- Log into github in the browser.   
- Then, visit https://drone.cpp.al and log in (with github)  
  
The admin permissions will carry over.

---

## Comment 20

> Username: Hermann-SW  
> Created_at: 2026-01-03 16:23:25 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-3707171843  

Now that a new year hast started and more than a year passed — what is missing to get new planar_vertex_six_coloring() and this pull request approved?

---

## Review 21 [Commented]

> Username: jeremy-murphy  
> Created_at: 2026-01-04 22:13:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/387#pullrequestreview-3625194617  

📁 doc/undirected_graph_constant_time_edge_add_and_remove.html

```diff
  18 |+ <H1><A NAME="sec:undirected-graph-class"></A>
  19 |+ <pre>
  20 |+ undirected_graph_constant_time_edge_add_and_remove&lt;VertexProp, EdgeProp, GraphProp&gt;
```

> Username: jeremy-murphy  
> Created_at: 2026-01-04 22:13:21 UTC  
> Url: https://github.com/boostorg/graph/pull/387#discussion_r2659964027  

I'm sure we can find a better name than this. We generally don't name things by listing the efficiency of operations.


---

## Comment 22

> Username: jeremy-murphy  
> Created_at: 2026-01-05 00:03:37 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-3708553050  

I need to figure out how to build the documentation in order to preview your changes... I haven't done that for years and have forgotten the arcane ritual. If you recall what it is, do let me know, thank you.  :)

---

## Comment 23

> Username: Hermann-SW  
> Created_at: 2026-01-05 07:25:09 UTC  
> Updated_at: 2026-01-05 07:25:41 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-3709234285  

Hi Jeremy,  
this is in the easy HTML part of the doc.  
From 11/9/24 comment the only affected doc files are:  
- doc/table_of_contents.html  
- doc/planar_vertex_six_coloring.html  
- doc/figs/planar_vertex_coloring_with_5.png  
  
The 2nd and 3rd are additions, no modifications and should be OK.  
The 1st is small modification of table of contents only.  
  
I think that HTML doc like this does not need to be build, but can be wrong with that. I have no experience in building the non-HTML boost doc.

---

## Comment 24

> Username: jeremy-murphy  
> Created_at: 2026-01-05 23:22:21 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-3712445739  

By the way, there is an unintentionally hidden bibliography file that you should add your citation to and put a reference to it in the algorithm's documentation. See strongly connected components for an example.

---

## Review 25 [Commented]

> Username: jeremy-murphy  
> Created_at: 2026-01-05 23:43:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/387#pullrequestreview-3628770562  

📁 include/boost/graph/planar_vertex_six_coloring.hpp

```diff
  79 |+   Theoretical Computer Science 1991
  80 |+   https://dl.acm.org/doi/10.1016/0304-3975%2891%2990020-3
  81 |+ */
```

> Username: jeremy-murphy  
> Created_at: 2026-01-05 23:43:02 UTC  
> Url: https://github.com/boostorg/graph/pull/387#discussion_r2663113185  

Make this a standalone algorithm with unit tests, i.e. change the name of this file and function to `five_bounded_acyclic_orientation` or something and don't call `sequential_vertex_coloring` at the end.  
Most of your unit tests should be for this function.  
  
Then it's just a matter of making a file with this name again that just does something like   
  
```  
typename property_traits< ColorMap >::value_type   
planar_vertex_six_coloring(const VertexListGraph& G, ColorMap color)   
{  
    auto rev = five_bounded_acyclic_orientation(G, color);  
    return sequential_vertex_coloring(G, make_container_vertex_map(rev, G), color);  
}  
```  
  
  
I'm not sure if your algorithm should return `rev` or something else yet, but essentially, `five_bounded_acyclic_orientation` should only do the minimum of what is required to achieve its purpose. Anything extra for graph coloring should go in the `planar_vertex_six_coloring` function.


---

## Comment 26

> Username: jeremy-murphy  
> Created_at: 2026-01-06 00:28:15 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-3712575419  

> @grisumbras @jeremy-murphy This pull request consists of 14 new or changed files. Since I created the pull request 6 weeks ago I have learned much more on how to do things better with Boost graph.  
  
Apologies for not replying earlier; Boost.Graph work has to come after a lot of other things in my life.  
  
Something to bare in mind with generic algorithm work is that the efficiency (often mistakenly called complexity) depends on two things: i) the algorithm's logic, and ii) the choice of data structure it is applied to. The second point is especially relevant in Boost.Graph, where not only do we have `adjacency_list` vs `adjacency_matrix`, but the former's edge and vertex lists are `vector` vs `list`. Generic algorithms ideally work on any data structure and simply inherit the underlying efficiency. That said, we can specialize algorithms using graph traits if it enables a novel and efficient implementation, which you might need in this case to achieve the ideal O(|V|). Keep that in mind with everything else I say.  
  
> Before I do the changes proposed, I want to ask you whether I should do the code improvement or not.  
>   
> These 3 files are not needed anymore:  
>   
> * doc/undirected_graph_constant_time_edge_add_and_remove.html  
> * example/undirected_graph_constant_time_edge_add_and_remove.cpp  
> * include/boost/graph/undirected_graph_constant_time_edge_add_and_remove.hpp  
  
That's good.  
  
> The changes in these two deep graph files can be reverted as they are not needed anymore:  
>   
> * include/boost/graph/detail/adjacency_list.hpp  
> * include/boost/graph/undirected_graph.hpp  
  
That's also good.  
  
> The pull request would then consist of these 9 files only:  
>   
> * include/boost/graph/planar_vertex_six_coloring.hpp  
> * example/planar_vertex_six_coloring.cpp  
> * test/Jamfile.v2  
> * test/planar_vertex_six_coloring.cpp  
> * test/planar_input_graphs/pentakis_dodecahedron.leda  
> * test/planar_input_graphs/maximal_planar_1000.leda  
> * doc/table_of_contents.html  
> * doc/planar_vertex_six_coloring.html  
> * doc/figs/planar_vertex_coloring_with_5.png  
>   
> So how does the simpification work? New class o1_edge_visitor derived from "edge_index_update_visitor" allows for the needed O(1) remove_edge(). I created a complete self-contained demo in this gist (with comments) for review: https://gist.github.com/Hermann-SW/539d2396e567a2ee26f4e0213f3b5fe7  
>   
>   
> There is only one use of "g.impl()" in new visit_edge() that removes an edge in O(1) by moving both out_edge_iterators of the edge to the fist positions in the out_edge_lists.  
>   
> Use of "g.impl()" is essential, without no O(1) remove_edge is possible. Is that OK?  
  
I'll have a look asap. This is where you might need to specialize implementation on graph traits. Ideally the unit tests should demonstrate the algorithm working on a variety of edge and vertex list types.  
  
> I tried to use "std::prev()" in line 49, but then the code just hangs indefinitely, so I cannot use it?  
  
That's very weird and needs investigation.

---

## Comment 27

> Username: Hermann-SW  
> Created_at: 2026-01-06 08:01:32 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-3713585805  

@jeremy-murphy  
Thanks, so I will do the proposed changes and todos from you. It might take some time since semester has started, but mid February after written examinations there will be a two month long semester break.  
  
(I am in early retirement at age of 60 and started studying Mathematics at University of Heidelberg, with nice computing center in basement of our house and challenging targets ;-)  https://stamm-wilbrandt.de/en/#future)

---

## Comment 28

> Username: jeremy-murphy  
> Created_at: 2026-01-06 08:27:24 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-3713672230  

That sounds awfully fun and I mean that quite earnestly.

---

## Comment 29

> Username: Hermann-SW  
> Created_at: 2026-01-06 08:40:07 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-3713710330  

I had to stop work on counting primes of form k²+(k+1)² up to 10^11 (first parallel code finished in 14:28h on 192 core server) . Had to traverse a directed acyclic graph of height 40 billion(!) for that. Not a boost graph (implicitly computed while being traversed), and using __int128_t was enough to avoid overflow (so no GMP nor Boost multiprecision):  
https://pari.math.u-bordeaux.fr/archives/pari-users-2601/msg00002.html

---

## Comment 30

> Username: Hermann-SW  
> Created_at: 2026-01-06 12:52:41 UTC  
> Url: https://github.com/boostorg/graph/pull/387#issuecomment-3714593235  

Related to this pull request subject, I did need planar 6 coloring some time ago, for coloring faces of convex hull computed with qhull software and displayed with geomview. I used my 6 coloring algorithm available in my (non-Boost) planar graph playground repo. Since the coloring guarantees different colors for each edge of the convex hull, I could stop drawing the edges which is important for many faces convex hull:  
https://stamm-wilbrandt.de/en/#long-term_objective  
Using up to 6 colors instead of 4 does not hurt:  
<img width="541" height="526" alt="image" src="https://github.com/user-attachments/assets/d29dd6e3-e843-4684-aec8-98ad3c25bb01" />

---
