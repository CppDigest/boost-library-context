# #407 Replace std::deque with std::vector for Lengauer-Tarjan [Merged]

> Username: samolisov  
> Created at: 2024-12-19 10:42:11 UTC  
> Updated at: 2024-12-20 21:27:49 UTC  
> Merged at: 2024-12-20 21:27:48 UTC  
> Closed at: 2024-12-20 21:27:48 UTC  
> Url: https://github.com/boostorg/graph/pull/407  

Issue: #383

---

## Comment 1

> Username: samolisov  
> Created_at: 2024-12-19 10:42:18 UTC  
> Url: https://github.com/boostorg/graph/pull/407#issuecomment-2553387318  

I use the following benchmark: [dominator_tree_benchmark.cpp](https://github.com/samolisov/boost.graph/blob/dominator_tree_benchmark/test/dominator_tree_benchmark.cpp)  
  
On my machine (32 X 1792.7 MHz CPU s with hyper-threading and almost zero Load Average, Ubuntu 20.4) the report is the following (we may use it as a base-line:  
  
```  
Running ./dominator_tree_benchmark  
Run on (64 X 1792.7 MHz CPU s)  
CPU Caches:  
  L1 Data 32 KiB (x32)  
  L1 Instruction 32 KiB (x32)  
  L2 Unified 512 KiB (x32)  
  L3 Unified 16384 KiB (x8)  
Load Average: 0.00, 0.01, 0.00  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
Tarjan's paper (vertex list)                  1545 ns         1545 ns       453859  
Tarjan's paper  (vertex vector)               1519 ns         1519 ns       461328  
Appel. fig. 19.8 (vertex list)                1587 ns         1587 ns       439286  
Appel. fig. 19.8  (vertex vector)             1489 ns         1488 ns       473160  
Muchnick. fig. 8.18 (vertex list)              870 ns          870 ns       805296  
Muchnick. fig. 8.18  (vertex vector)           847 ns          847 ns       833885  
Cytron's paper, fig. 9 (vertex list)          1812 ns         1812 ns       391492  
Cytron's paper, fig. 9  (vertex vector)       1651 ns         1651 ns       421622  
From a code, 186 BBs (vertex list)           22388 ns        22386 ns        29279  
From a code, 186 BBs (vertex vector)         19391 ns        19389 ns        35854  
```  
  
After replacing std::deque with std::vector as it was suggested by @jeremy-murphy (commit ), the numbers are the following:  
  
```  
----------------------------------------------------------------------------------  
Benchmark                                        Time             CPU   Iterations  
----------------------------------------------------------------------------------  
Tarjan's paper (vertex list)                   944 ns          944 ns       747521  
Tarjan's paper  (vertex vector)                852 ns          852 ns       822843  
Appel. fig. 19.8 (vertex list)                 956 ns          956 ns       729393  
Appel. fig. 19.8  (vertex vector)              865 ns          865 ns       807835  
Muchnick. fig. 8.18 (vertex list)              560 ns          560 ns      1252412  
Muchnick. fig. 8.18  (vertex vector)           543 ns          543 ns      1306887  
Cytron's paper, fig. 9 (vertex list)          1164 ns         1164 ns       602748  
Cytron's paper, fig. 9  (vertex vector)       1054 ns         1054 ns       666930  
From a code, 186 BBs (vertex list)           12750 ns        12749 ns        55535  
From a code, 186 BBs (vertex vector)         11284 ns        11283 ns        62585  
```  
Here we can see *about 40%* speedup for the "large" cases (for CFGs with 186 basic blocks).

---
