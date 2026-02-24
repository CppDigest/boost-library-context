# #408 Use vector-of-structs of preds/semi for Lengauer-Tarjan [Merged]

> Username: samolisov  
> Created at: 2024-12-21 04:44:50 UTC  
> Updated at: 2025-03-04 13:19:31 UTC  
> Merged at: 2025-03-04 13:19:30 UTC  
> Closed at: 2025-03-04 13:19:30 UTC  
> Url: https://github.com/boostorg/graph/pull/408  

Closes #383

---

## Comment 1

> Username: samolisov  
> Created_at: 2024-12-21 04:49:47 UTC  
> Updated_at: 2025-03-02 04:52:45 UTC  
> Url: https://github.com/boostorg/graph/pull/408#issuecomment-2557996237  

I use the following benchmark: [dominator_tree_benchmark.cpp](https://github.com/samolisov/boost.graph/blob/dominator_tree_benchmark/test/dominator_tree_benchmark.cpp)  
  
On my machine (32 X 1792.7 MHz CPU s with hyper-threading and almost zero Load Average, Ubuntu 20.4) the report is the following (we may use the state after merging #407 as a base-line:  
  
```  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
Tarjan's paper (vertex list)                   934 ns          934 ns       748347  
Tarjan's paper  (vertex vector)                845 ns          845 ns       830574  
Appel. fig. 19.8 (vertex list)                 960 ns          959 ns       731191  
Appel. fig. 19.8  (vertex vector)              860 ns          860 ns       813827  
Muchnick. fig. 8.18 (vertex list)              561 ns          560 ns      1248586  
Muchnick. fig. 8.18  (vertex vector)           538 ns          538 ns      1302725  
Cytron's paper, fig. 9 (vertex list)          1145 ns         1145 ns       613263  
Cytron's paper, fig. 9  (vertex vector)       1046 ns         1046 ns       674659  
From a code, 186 BBs (vertex list)           12938 ns        12937 ns        54742  
From a code, 186 BBs (vertex vector)         11528 ns        11527 ns        62319  
```  
  
After implementing a "vector-of-structs" solution, the numbers are the following:  
  
```  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
Tarjan's paper (vertex list)                   919 ns          919 ns       768302  
Tarjan's paper  (vertex vector)                835 ns          835 ns       838532  
Appel. fig. 19.8 (vertex list)                 944 ns          944 ns       739354  
Appel. fig. 19.8  (vertex vector)              854 ns          854 ns       825316  
Muchnick. fig. 8.18 (vertex list)              527 ns          527 ns      1285818  
Muchnick. fig. 8.18  (vertex vector)           488 ns          488 ns      1433765  
Cytron's paper, fig. 9 (vertex list)          1101 ns         1101 ns       636063  
Cytron's paper, fig. 9  (vertex vector)       1024 ns         1024 ns       685137  
From a code, 186 BBs (vertex list)           12754 ns        12753 ns        54584  
From a code, 186 BBs (vertex vector)         11623 ns        11622 ns        61169  
```  
Here we can see *about 1%* speedup for the "large" cases (for CFGs with 186 basic blocks) and *about 10%* for small ones (*Muchnick. fig. 8.18*, 8 vertices).  
  
I'm thinking what to deal with the `semedom_` vector: whether should we put samedoms into the struct? The pattern is a little different so that some more experiments are required.

---

## Comment 2

> Username: samolisov  
> Created_at: 2024-12-21 05:20:48 UTC  
> Url: https://github.com/boostorg/graph/pull/408#issuecomment-2558003780  

Maybe a check on a larger graph (up to 1000 or 2000-3000) nodes is needed to ensure there is no regression for large inputs.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2024-12-21 06:09:42 UTC  
> Url: https://github.com/boostorg/graph/pull/408#issuecomment-2558016112  

Thanks for trying this change, pity it didn't yield anything significant. I still think it's a better logical design, so I'm happy to proceed with it, although I'd like to make a few style changes.   
For starters, I think we can just drop the set functions on the struct. More later.

---

## Comment 4

> Username: samolisov  
> Created_at: 2024-12-29 14:39:29 UTC  
> Url: https://github.com/boostorg/graph/pull/408#issuecomment-2564745044  

@jeremy-murphy Thank you for the suggestion, I've replaced every `set_` method with a direct writing to the corresponding field and remove the methods.  
  
Also, I added a benchmark for a huge (3000+ nodes) graph, on such graph I see the following situation. The baseline (code from the `develop` branch):  
  
```  
Huge Inlined Function (vertex list)         275707 ns       275683 ns         2531  
Huge Inlined Function (vertex vector)       236892 ns       236878 ns         2969  
```  
  
With the "cache-friendly" solution:  
  
```  
Huge Inlined Function (vertex list)         284871 ns       284855 ns         2495  
Huge Inlined Function (vertex vector)       251233 ns       251218 ns         2783  
```  
  
So, we can see even some performance degradation, up to *3-6%*.

---

## Review 5 [Changes requested]

> Username: jeremy-murphy  
> Created_at: 2025-02-25 00:09:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/408#pullrequestreview-2638599995  

Thanks for your patience!  
I'd love some changes about naming, etc and one change that might improve performance.   
Thank you!

📁 include/boost/graph/dominator_tree.hpp

```diff
 155 |             }
 140 |-             put(semiMap_, n, s);
 156 |+             preds< Graph >& preds_of_n = get(predsMap_, n);
```

> Username: jeremy-murphy  
> Created_at: 2025-02-24 22:34:41 UTC  
> Updated_at: 2025-02-25 00:09:56 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1968512003  

Please use `auto` in preference to repeating the type, or maybe `auto &` in this case, I can't remember the return type of `get`.

> Username: samolisov  
> Created_at: 2025-02-27 11:43:08 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1973417665  

As I remember, `auto` doesn't deduce a reference vs value anyway, so I put `auto &` here and some other places below, thank you for the suggestion.

---

📁 include/boost/graph/dominator_tree.hpp

```diff
  75 |+         , best(graph_traits< Graph >::null_vertex())
  76 |+         {
  77 |+         }
```

> Username: jeremy-murphy  
> Created_at: 2025-02-24 22:49:17 UTC  
> Updated_at: 2025-02-25 00:09:56 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1968529320  

Try removing this constructor altogether. I don't think it's required for correctness, although I might be wrong.   
I can't remember the rules, but I have a feeling that it would affect performance too.

> Username: samolisov  
> Created_at: 2025-02-27 11:42:33 UTC  
> Updated_at: 2025-02-27 11:42:34 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1973416783  

Without this constructor, no errors are detected by the tests. I used to manually define constructors in aggregates to only put them into containers before C++20 because all the STL implementations, I have, prefer the `()` syntax inside to create objects and this used to be incompatible with aggregate initialization (the `{}` syntax) what was fixed in C++20, as well as I remember.  
  
Only the concern I have, what the values will actually be used to initialize the `struct preds` members? Different values are used for `null_vertex()` through the codebase, usually this is zero, hence the absence of the constructor should lead to no problems. But sometimes this is not zero, for `AdjacencyMatrix` this is `std::numeric_limits< vertex_descriptor >::max()` as well as for the `AdjacencyList` where a vector is used to store the vertex list (the second template parameter):  
  
```c++  
template < class Graph, class Config, class Base >  
class vec_adj_list_impl : public adj_list_helper< Config, Base >  
{  
...  
    static vertex_descriptor null_vertex()  
    {  
        return (std::numeric_limits< vertex_descriptor >::max)();  
    }  
...  
}  
```  
   
There is a comparison with `null_vertex()` in the [`ancestor_with_lowest_semi_()` method](https://github.com/boostorg/graph/blob/develop/include/boost/graph/dominator_tree.hpp#L182):  
  
```c++  
if (preds_of_a.ancestor != graph_traits< Graph >::null_vertex()) {  
  ...  
}  
```  
  
I though this was a comparison with **no ancestor** but unfortunately this is a comparison with **no value has been calculated yet**, so the comparison with the sentinel (I checked this with initializing all three components of the `preds` struct to a value different than `null_vertex()` and assert the current value before every read. By the way, this looks as the only actual place where the being read value may still be set to the sentinel, all the other reads from the `preds` components are done after the corresponding writes).  
  
I've added test cases for *adjacency_list< listS, **vecS**, bidirectionalS >*. Also, I would like to add cases for `AdjacencyMatrix` but even though [the documentation](https://www.boost.org/doc/libs/1_87_0/libs/graph/doc/adjacency_matrix.html) says the class supports the `Bidirectional Graph` concept, I get the following error: `error: no viable conversion from 'boost::adj_matrix_traversal_tag' to 'boost::bidirectional_graph_tag'`.  
  
Unfortunately, we cannot just skip the initialization and use only the `Vertex` as the template parameter. This is why I left the `Graph` as the template parameters and moved the `null_vertex()` from the user-provided constructor to the field initialization (I believe this improves a readability a little bit):  
  
```c++  
  Vertex semi{graph_traits< Graph >::null_vertex()};  
  Vertex ancestor{graph_traits< Graph >::null_vertex()};  
  Vertex best{graph_traits< Graph >::null_vertex()};  
```  
  
Also, I benchmarked the cases where the test doesn't crash (with lists used to store vector lists) and even on the huge benchmark with more than 3000 vertices I see no much difference whether the vertices are initialized or not (actually, I even get the better result when they are initialized: the average of 3 subsequent runs took is in 0.6% fewer CPU counts):  
  
```  
Huge Inlined Function (vertex list)      275966 ns       275931 ns         2572  
Huge Inlined Function (vertex list)      279176 ns       279148 ns         2582  
Huge Inlined Function (vertex list)      275080 ns       275055 ns         2587  
```  
vs  
  
```  
Huge Inlined Function (vertex list)      276789 ns       276770 ns         2542  
Huge Inlined Function (vertex list)      275617 ns       275592 ns         2581  
Huge Inlined Function (vertex list)      272956 ns       272937 ns         2580  
```

> Username: jeremy-murphy  
> Created_at: 2025-02-28 07:16:41 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1974921426  

I just merged a PR that adds the `BidirectionalGraph` concept for `adjacency_matrix`.

> Username: samolisov  
> Created_at: 2025-02-28 07:38:27 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1974945221  

@jeremy-murphy Oh, thank you for the news. I'm going to play with `adjacency_matrix` again within a couple of days.

> Username: jeremy-murphy  
> Created_at: 2025-02-28 07:48:03 UTC  
> Updated_at: 2025-02-28 07:57:59 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1974955615  

OK, yeah I actually overlooked the fact that the baseline code does use `null_vertex()` to initialize some of the predecessor information, so my suggestion was fairly invalid!

---

📁 include/boost/graph/dominator_tree.hpp

```diff
  77 |+         }
  78 |+ 
  79 |+         typedef typename graph_traits< Graph >::vertex_descriptor Vertex;
```

> Username: jeremy-murphy  
> Created_at: 2025-02-24 22:50:07 UTC  
> Updated_at: 2025-02-25 00:09:56 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1968530121  

Please change this to the modern `using` syntax.

---

📁 include/boost/graph/dominator_tree.hpp

```diff
  68 |+     // structure let us organize a "vector of structs" what improves cache
  69 |+     // efficiency.
  70 |+     template < class Graph > struct preds
```

> Username: jeremy-murphy  
> Created_at: 2025-02-24 22:53:26 UTC  
> Updated_at: 2025-02-25 00:09:56 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1968533007  

You can change this to just take a `Vertex` parameter after removing the constructor.  
  
`pred` is too ambiguous; I actually thought it meant "predicate" until I remembered that it's a predecessor map. :) But "predecessor" is not actually the type anyway.  It's when it's used to store predecessors that we give the variable that name. The type is just "Vertex triple" because it's identical to a `std::tuple` of three Vertex elements. I would actually be ok with `Vertex_triple` as a type but maybe you can think of something better.  
  
Finally, we usually put a blank line between template and class name.

> Username: samolisov  
> Created_at: 2025-02-27 11:42:39 UTC  
> Updated_at: 2025-02-27 11:42:59 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1973416885  

> You can change this to just take a Vertex parameter after removing the constructor.  
  
I would like to preserve the `Graph` template parameter because I believe the `null_vertex()` is still needed as the sentinel, please have a look at the my answer on the previous comment.  
  
> I would actually be ok with Vertex_triple as a type but maybe you can think of something better.  
  
This structure looks as very technically, actually we've added it just to be more cache friendly, so, I agree with `vertex_triple` and leaving the my comment about why the structure is needed at all.  
  
> Finally, we usually put a blank line between template and class name.  
  
Done, thank you! By the way, the `.clang-format` in the root of the `libs/graph` forces my IDE to put the `template` and class name on the same line. I've added the `BreakTemplateDeclarations: Yes` option to `.clang-format`.

> Username: jeremy-murphy  
> Created_at: 2025-02-28 07:13:28 UTC  
> Updated_at: 2025-02-28 07:13:29 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1974918728  

Yes, I'll read and reply to the use of `null_vertex()` in another thread, and I agree that if you still need it then yes it has to stay as `Graph`.  
  
Sorry about the `.clang-format`, I didn't create it and I really have to get around to fixing it. Thanks for making a small improvement!

> Username: jeremy-murphy  
> Created_at: 2025-03-03 22:10:47 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1978315569  

Sorry, I should have been clearer in my previous message, could you please change `preds` to `predeccesor`, no plural. Thank you!


---

## Comment 6

> Username: samolisov  
> Created_at: 2025-02-27 14:42:29 UTC  
> Url: https://github.com/boostorg/graph/pull/408#issuecomment-2688165584  

I've compared the baseline (the current `develop` branch) and the PR again on the `Huge Inlined Function (vertex vector)` benchmark and gathered the cache-references and cache-misses count for my CPU (AMD EPYC 7502P 32-Core Processor, 1793.628 MHz, 512 KB cache). The benchmark has been compiled with clang 20 rc2 and `-O3` is used as the optimization level.  
  
The command:  
  
```sh  
$ perf stat -B -e cache-references,cache-misses,cycles,instructions,branches,faults,migrations ./dominator_tree_benchmark_20_O3_baseline --benchmark_filter="Huge Inlined Function \(vertex vector\)"  
```  
  
Baseline:  
  
```  
       356,911,462      cache-references  
        78,879,574      cache-misses              #   22.101 % of all cache refs  
     1,501,651,461      branches  
  
       359,839,124      cache-references  
        78,829,953      cache-misses              #   21.907 % of all cache refs  
     1,500,906,629      branches  
  
      365,086,044      cache-references  
        82,171,256      cache-misses              #   22.507 % of all cache refs  
     1,521,432,528      branches  
```  
  
The final variant:  
  
```  
       343,820,294      cache-references  
        78,653,556      cache-misses              #   22.876 % of all cache refs  
     1,475,058,434      branches  
  
       338,131,001      cache-references  
        76,760,089      cache-misses              #   22.701 % of all cache refs  
     1,462,350,259      branches  
  
       332,844,432      cache-references  
        77,863,111      cache-misses              #   23.393 % of all cache refs  
     1,467,184,206      branches  
```  
The final variant leads to about 1 percentage point more cache misses, from another point of view and I have no idea why, it leads to about 3% fewer branches.  
  
I have no answer yet why the final variant with using a vector of structs leads to even a little bit but to more cache misses on our workload (the implementation of the algorithm). My hypothesis is some irregularity in the algorithm itself: when we do not scan every vertex with its triple one by one but jumps from one to another over the half of the array but this is my attempt to just guess the answer only and not a result of any investigation. But the question is interesting on its own, as we can see, not every workload can be made better from the performance point of view just by using the vector of structs or struct of vectors. Anyway I would like thank you for the initial hypothesis to use the vector of structs: it gives me a good task to play with.  
  
Also, the average of the subsequent 3 runs for the benchmark: 239734 ns (the final version) vs 236870 ns (baseline), baseline is slightly better, in 1.2% (interesting that the `Huge Inlined Function (vertex list)` benchmark where a list is used to store vertices demonstrates exactly the same ratio).

---

## Review 7 [Commented]

> Username: jeremy-murphy  
> Created_at: 2025-02-28 07:57:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/408#pullrequestreview-2649962696  

I'm happy with the code change even with the apparent cost to performance. I'm curious whether the same performance change happens across compilers and CPUs. If you have time, please try GCC as well.  
I'll wait for you adjacency_matrix changes or a comment from you that you're done before I finalize the review.

📁 include/boost/graph/dominator_tree.hpp

```diff
 146 |-             get(bestMap_, n) = n;
 158 |+             preds_of_n.ancestor = p;
 159 |+             preds_of_n.best = n;
```

> Username: jeremy-murphy  
> Created_at: 2025-02-28 07:28:34 UTC  
> Updated_at: 2025-02-28 07:57:59 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1974934548  

You would expect that the optimizer is doing this, but try changing this code to use one assignment, like so: `preds_of_n = {s, p, n};`  
I would be happy with that change regardless of performance outcome.

> Username: samolisov  
> Created_at: 2025-03-01 13:27:20 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1976407764  

@jeremy-murphy Thank you for the suggestion. I've tried GCC 11.4 (the latest version I have on a system available for me) with the optimization level `-O3` and `-march=native` (I didn't apply this argument to my clang experiments, so, no one please compare gcc vs clang on this results!). The results are the following (I also added the adjacency matrix there).  
  
The baseline (the code from the `develop` branch):  
```  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
Tarjan's paper (vertex list)                   924 ns          923 ns       759068  
Tarjan's paper  (vertex vector)                894 ns          894 ns       780867  
Appel. fig. 19.8 (vertex list)                 955 ns          954 ns       734470  
Appel. fig. 19.8  (vertex vector)              936 ns          935 ns       746162  
Muchnick. fig. 8.18 (vertex list)              582 ns          582 ns      1177545  
Muchnick. fig. 8.18  (vertex vector)           614 ns          614 ns      1144572  
Cytron's paper, fig. 9 (vertex list)          1223 ns         1223 ns       570449  
Cytron's paper, fig. 9  (vertex vector)       1162 ns         1161 ns       607144  
From a code, 186 BBs (vertex list)           12887 ns        12881 ns        54110  
From a code, 186 BBs (vertex vector)         11740 ns        11736 ns        60299  
Huge Inlined Function (vertex list)         273151 ns       273016 ns         2570  
Huge Inlined Function (vertex vector)       252956 ns       252848 ns         2767  
```  
  
The current version (before I had a look at the latest proposed changes with `preds_of_n = {s, p, n}`):  
  
```  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
Tarjan's paper (vertex list)                   861 ns          860 ns       814789  
Tarjan's paper  (vertex vector)                826 ns          826 ns       844254  
Appel. fig. 19.8 (vertex list)                 920 ns          920 ns       759339  
Appel. fig. 19.8  (vertex vector)              876 ns          876 ns       799323  
Muchnick. fig. 8.18 (vertex list)              508 ns          508 ns      1386601  
Muchnick. fig. 8.18  (vertex vector)           508 ns          507 ns      1375152  
Cytron's paper, fig. 9 (vertex list)          1171 ns         1171 ns       604467  
Cytron's paper, fig. 9  (vertex vector)       1142 ns         1142 ns       616078  
From a code, 186 BBs (vertex list)           12592 ns        12587 ns        55086  
From a code, 186 BBs (vertex vector)         11549 ns        11545 ns        59611  
Huge Inlined Function (vertex list)         274498 ns       274396 ns         2550  
Huge Inlined Function (vertex vector)       253776 ns       253690 ns         2750  
```  
  
The spread of the gathered values is larger today than usually, maybe the OS upgrade makes sense, I have no idea but the numbers of iterations change from 2730 to 2750 for the final solution.   
  
For the *adjacency matrix*, the current solution is a **little bit** but faster:  
  
baseline:  
```  
--------------------------------------------------------------------------  
Benchmark                                Time             CPU   Iterations  
--------------------------------------------------------------------------  
Tarjan's paper (matrix)               1388 ns         1387 ns       504212  
Appel. fig. 19.8 (matrix)             1412 ns         1412 ns       495507  
Muchnick. fig. 8.18 (matrix)           722 ns          721 ns       975675  
Cytron's paper, fig. 9 (matrix)       1609 ns         1609 ns       433872  
From a code, 186 BBs (matrix)        50667 ns        50649 ns        13820  
Huge Inlined Function (matrix)    23756790 ns     23746371 ns           29  
```  
  
the PR:  
```  
--------------------------------------------------------------------------  
Benchmark                                Time             CPU   Iterations  
--------------------------------------------------------------------------  
Tarjan's paper (matrix)               1236 ns         1236 ns       564395  
Appel. fig. 19.8 (matrix)             1285 ns         1285 ns       543739  
Muchnick. fig. 8.18 (matrix)           661 ns          661 ns      1071957  
Cytron's paper, fig. 9 (matrix)       1554 ns         1554 ns       447753  
From a code, 186 BBs (matrix)        50213 ns        50199 ns        13927  
Huge Inlined Function (matrix)    23586880 ns     23575685 ns           30  
  
```  
  
By the way, GCC doesn't blame me for stack exhausting and successfully compile even the last two cases where I use graphs with 3000+ nodes. Clang 19 fails on such code while Clang 20 rc2 compiles it but it takes a lot of time (about 20-30 minutes).

> Username: samolisov  
> Created_at: 2025-03-01 14:01:18 UTC  
> Url: https://github.com/boostorg/graph/pull/408#discussion_r1976425681  

> You would expect that the optimizer is doing this, but try changing this code to use one assignment, like so: `preds_of_n = {s, p, n};` I would be happy with that change regardless of performance outcome.  
  
I've applied the proposal exactly as you suggest it. Also, we can write:  
  
```c++  
put(predsMap_, n, vertex_triple<Graph>{s, p, n})  
```  
  
But I believe such code has worser readability due to this ugly `vertex_triple<Graph>{...}`. Unfortunately, it looks as we cannot just write `{s, p, n}` here because the `put` function is not able to deduce the type of the argument. From the performance point of view, both variants have no difference.  
  
The final numbers for GCC 11.4 with `-O3 -march=native`:  
  
```  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
Tarjan's paper (vertex list)                   866 ns          865 ns       803960  
Tarjan's paper  (vertex vector)                818 ns          818 ns       854843  
Appel. fig. 19.8 (vertex list)                 913 ns          913 ns       764869  
Appel. fig. 19.8  (vertex vector)              856 ns          856 ns       818267  
Muchnick. fig. 8.18 (vertex list)              506 ns          506 ns      1381786  
Muchnick. fig. 8.18  (vertex vector)           491 ns          490 ns      1435080  
Cytron's paper, fig. 9 (vertex list)          1125 ns         1125 ns       615688  
Cytron's paper, fig. 9  (vertex vector)       1110 ns         1109 ns       627264  
From a code, 186 BBs (vertex list)           12564 ns        12559 ns        55650  
From a code, 186 BBs (vertex vector)         11576 ns        11571 ns        60228  
Huge Inlined Function (vertex list)         271693 ns       271599 ns         2570  
Huge Inlined Function (vertex vector)       252105 ns       252019 ns         2778  
  
--------------------------------------------------------------------------  
Benchmark                                Time             CPU   Iterations  
--------------------------------------------------------------------------  
Tarjan's paper (matrix)               1241 ns         1241 ns       562975  
Appel. fig. 19.8 (matrix)             1284 ns         1284 ns       517405  
Muchnick. fig. 8.18 (matrix)           659 ns          658 ns      1054778  
Cytron's paper, fig. 9 (matrix)       1529 ns         1529 ns       456577  
From a code, 186 BBs (matrix)        50562 ns        50545 ns        13997  
Huge Inlined Function (matrix)    23890642 ns     23879723 ns           29  
```


---

## Comment 8

> Username: samolisov  
> Created_at: 2025-03-01 14:09:05 UTC  
> Url: https://github.com/boostorg/graph/pull/408#issuecomment-2692251410  

I believe I'm done from my side (when the CI will find no errors). @jeremy-murphy could you have a look again?

---

## Review 9 [Changes requested]

> Username: jeremy-murphy  
> Created_at: 2025-03-03 22:11:39 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/408#pullrequestreview-2655471566  

Just requested a small change in variable name and then it's good to merge.

---

## Comment 10

> Username: samolisov  
> Created_at: 2025-03-04 10:43:44 UTC  
> Url: https://github.com/boostorg/graph/pull/408#issuecomment-2697045287  

> Just requested a small change in variable name and then it's good to merge.  
  
I've renamed `preds_` -> `pred_`, `predsMap_` -> `predMap_`, and all of `preds_of_...` -> `pred_of_...`. Thank you for the suggestion.

---

## Review 11 [Approved]

> Username: jeremy-murphy  
> Created_at: 2025-03-04 13:17:26 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/graph/pull/408#pullrequestreview-2657439669  

Thanks so much for making this improvement to your previous change, I think it's a good improvement in general even if it's not perfect.

---
