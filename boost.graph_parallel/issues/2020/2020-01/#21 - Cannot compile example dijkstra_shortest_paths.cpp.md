# #21 - Cannot compile example dijkstra_shortest_paths.cpp [Closed]

> Username: giomasce  
> Created at: 2020-01-02 16:30:14 UTC  
> Updated at: 2021-09-11 12:34:15 UTC  
> Closed at: 2021-05-31 17:16:36 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/21  

Hi. I am trying to compile the example file `dijkstra_shortest_paths.cpp`, but it seems to fail. Is this a bug in the library, or is the example outdated? Or am I missing anything else?  
  
I enter the directory `libs/graph_parallel/example`, then:  
```  
$ cat user-config.jam   
using mpi ;  
$ b2 --user-config=user-config.jam  
warning: mismatched versions of Boost.Build engine and core  
warning: Boost.Build engine (b2) is 4.0.0  
warning: Boost.Build core (at /home2/giovanni2/packages/boost/upstream2/tools/build/src) is 4.1-git  
warning: No toolsets are configured.  
warning: Configuring default toolset "gcc".  
warning: If the default is wrong, your build may not work correctly.  
warning: Use the "toolset=xxxxx" option to override our guess.  
warning: For more configuration options, please consult  
warning: http://boost.org/boost-build2/doc/html/bbv2/advanced/configuration.html  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
warning: non-free usage requirements <threading>multi ignored  
warning: in main-target mpi at /home2/giovanni2/packages/boost/upstream2/libs/graph_parallel/example/user-config.jam:1  
warning: non-free usage requirements <threading>multi ignored  
warning: in main-target boost_mpi at ../../../libs/mpi/build/Jamfile.v2:120  
...patience...  
...patience...  
...patience...  
...found 8840 targets...  
...updating 2 targets...  
gcc.compile.c++ ../../../bin.v2/libs/graph_parallel/example/gcc-9/debug/threading-multi/visibility-hidden/dijkstra_shortest_paths.o  
In file included from ../../../boost/graph/distributed/dijkstra_shortest_paths.hpp:20,  
                 from ../../../boost/graph/dijkstra_shortest_paths.hpp:595,  
                 from dijkstra_shortest_paths.cpp:19:  
../../../boost/graph/distributed/crauser_et_al_shortest_paths.hpp:189:13: error: ‘relaxed_heap’ does not name a type  
  189 |     typedef relaxed_heap<vertex_descriptor, dist_queue_compare_type,  
      |             ^~~~~~~~~~~~  
../../../boost/graph/distributed/crauser_et_al_shortest_paths.hpp:203:13: error: ‘relaxed_heap’ does not name a type  
  203 |     typedef relaxed_heap<vertex_descriptor, out_queue_compare_type,  
      |             ^~~~~~~~~~~~  
../../../boost/graph/distributed/crauser_et_al_shortest_paths.hpp:217:13: error: ‘relaxed_heap’ does not name a type  
  217 |     typedef relaxed_heap<vertex_descriptor, in_queue_compare_type,  
      |             ^~~~~~~~~~~~  
../../../boost/graph/distributed/crauser_et_al_shortest_paths.hpp:224:22: error: ‘dist_queue_type’ has not been declared  
  224 |     typedef typename dist_queue_type::size_type  size_type;  
      |                      ^~~~~~~~~~~~~~~  
../../../boost/graph/distributed/crauser_et_al_shortest_paths.hpp:225:22: error: ‘dist_queue_type’ has not been declared  
  225 |     typedef typename dist_queue_type::value_type value_type;  
      |                      ^~~~~~~~~~~~~~~  
../../../boost/graph/distributed/crauser_et_al_shortest_paths.hpp:403:5: error: ‘dist_queue_type’ does not name a type; did you mean ‘distance_type’?  
  403 |     dist_queue_type           dist_queue;  
      |     ^~~~~~~~~~~~~~~  
      |     distance_type  
../../../boost/graph/distributed/crauser_et_al_shortest_paths.hpp:404:5: error: ‘out_queue_type’ does not name a type  
  404 |     out_queue_type            out_queue;  
      |     ^~~~~~~~~~~~~~  
../../../boost/graph/distributed/crauser_et_al_shortest_paths.hpp:405:5: error: ‘in_queue_type’ does not name a type  
  405 |     in_queue_type             in_queue;  
      |     ^~~~~~~~~~~~~  
[many other error messages]  
```  
  
It seems that `relaxed_heap` was deprecated. Maybe `graph_parallel` should stop making use of them? Thanks for your help.

---

## Comment 1

> Username: giomasce  
> Created at: 2020-01-02 16:31:34 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/21#issuecomment-570261219  

Forgot to say that I am using an up-to-date Debian unstable system, and compiling from a up-to-date boost repository.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-05-31 17:16:36 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/21#issuecomment-851604767  

This was mostly caused by changes elsewhere - now fixed in develop.

---

## Comment 3

> Username: ginggs  
> Created at: 2021-09-11 12:34:15 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/21#issuecomment-917400050  

@giomasce it seems this was fixed by https://github.com/boostorg/graph_parallel/commit/a6f794d69ed7763b79500147d495b0d347afe919
