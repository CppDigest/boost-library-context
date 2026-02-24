# #406 Add a benchmark for the Lengauer-Tarjan dominator tree algorithm [Open]

> Username: samolisov  
> Created at: 2024-12-19 04:13:32 UTC  
> Updated at: 2025-02-27 04:48:31 UTC  
> Url: https://github.com/boostorg/graph/pull/406  

Issue: #383

---

## Comment 1

> Username: samolisov  
> Created_at: 2024-12-19 04:18:23 UTC  
> Updated_at: 2024-12-19 04:24:56 UTC  
> Url: https://github.com/boostorg/graph/pull/406#issuecomment-2552739814  

I use numerous small test cases (10-14 vertices) from the `dominator_tree_test.cpp` test as well as a 10x times larger example - a CFG from a real code (up to 200 vertices).  
  
Unfortunately, I'm not familiar with a CMake-based infrastructure (if any exists at all) to build and test the boost.graph library. This is why I've added a short instruction how to compile the benchmark directly into the `dominator_tree_benchmark.cpp` source file.  
  
On my machine (64 X 1792.7 MHz CPU s with almost zero Load Average) the report is the following (we may use it as a base-line for issue #383):  
  
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
  
After replacing `std::deque` with `std::vector` as it was suggested by @jeremy-murphy, the numbers are the following:  
  
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
(I checked for `From a code, 186 BBs (vertex vector)`, `std::vector` wins about **40%**, not so bad!)

---

## Comment 2

> Username: samolisov  
> Created_at: 2024-12-19 04:26:03 UTC  
> Url: https://github.com/boostorg/graph/pull/406#issuecomment-2552747273  

@jeremy-murphy Could you have a look? Unfortunately, I'm not able to edit the Reviewers list.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2024-12-21 07:21:35 UTC  
> Url: https://github.com/boostorg/graph/pull/406#issuecomment-2558033150  

I have an improvement to the CMake build in a PR of my own, so I'll try to merge that ASAP.  After that's merged it will be easier to incorporate your benchmark.   
As a rule, I want to separate tests and benchmarks physically, but I haven't decided exactly how yet.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2025-02-25 09:32:33 UTC  
> Url: https://github.com/boostorg/graph/pull/406#issuecomment-2681310105  

The GitHub app doesn't render the new file, unfortunately, so it will take me longer to review, sorry.

---

## Comment 5

> Username: samolisov  
> Created_at: 2025-02-25 09:39:37 UTC  
> Url: https://github.com/boostorg/graph/pull/406#issuecomment-2681342970  

@jeremy-murphy If you believe such large files with tests/benchmark should not take place in the sources, please let me know, I'll remove the largest benchmark and add an instruction how to generate this code. Actually, the code for the huge benchmark case was generated from some LLVM IR.

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2025-02-25 09:43:10 UTC  
> Url: https://github.com/boostorg/graph/pull/406#issuecomment-2681352892  

I presume it's a problem with the app, but I won't know for sure until I have a chance to check it on something other than my mobile phone.

---
