# #139 k-ary tree data structure [Open]

> Username: jeremy-murphy  
> Created at: 2018-11-26 12:52:47 UTC  
> Updated at: 2023-05-25 00:34:19 UTC  
> Url: https://github.com/boostorg/graph/pull/139  

This is a long way from finished but it might be useful to start a discussion about it early.  
  
First thing: this is not a true pull request. This is just a proof-of-concept demonstration.  
It is not for actually merging. That's why I've included my CMakeList build files, so you can check it out and play with it exactly as I am. I recommend you compile and run the benchmarks and satisfy yourself that they are valid.  
  
I'm not particularly interested in a detailed critique of the code at this point. An actual implementation to be merged might look vastly different.  
  
I am interested in high level ideas of design, interface and mathematical faithfulness to what a _k_-ary tree is (and accurate benchmarks). This is really about exploring what is possible.  
  
I know lots of things are incomplete. There may even be egregious bugs.  
  
This pertains to the discussion in issue #123, Tree class in BGL.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2018-11-26 12:59:37 UTC  
> Url: https://github.com/boostorg/graph/pull/139#issuecomment-441630326  

So anyway if you don't want to run the benchmarks yourself, here are mine:  
  
```  
2018-11-26 23:56:10  
Running /var/tmp/graph/RelWithDebInfo/benchmark/tree_algorithms  
Run on (8 X 3700 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 256K (x4)  
  L3 Unified 6144K (x1)  
Load Average: 1.27, 0.77, 0.84  
----------------------------------------------------------------------------------------------  
Benchmark                                                       Time           CPU Iterations  
----------------------------------------------------------------------------------------------  
BM_graph_isomorphism<bidirectional_binary_tree>/8             149 ns        149 ns    4756702  
BM_graph_isomorphism<bidirectional_binary_tree>/64           1319 ns       1319 ns     529859  
BM_graph_isomorphism<bidirectional_binary_tree>/512         10748 ns      10748 ns      65146  
BM_graph_isomorphism<bidirectional_binary_tree>/4096        86442 ns      86442 ns       8072  
BM_graph_isomorphism<bidirectional_binary_tree>/32768      711332 ns     711335 ns       1015  
BM_graph_isomorphism<bidirectional_binary_tree>/131072    2850611 ns    2850628 ns        237  
BM_graph_isomorphism<adjacency_list<>>/8                     2426 ns       2426 ns     300437  
BM_graph_isomorphism<adjacency_list<>>/64                   13105 ns      13105 ns      51929  
BM_graph_isomorphism<adjacency_list<>>/512                 383254 ns     383257 ns       1794  
BM_graph_isomorphism<adjacency_list<>>/4096              25632327 ns   25632419 ns         26  
BM_graph_isomorphism<adjacency_list<>>/8192             105986260 ns  105987424 ns          7  
BM_depth_first_visit_bidirectional_binary_tree/8               78 ns         78 ns    8997743  
BM_depth_first_visit_bidirectional_binary_tree/64             719 ns        719 ns     956775  
BM_depth_first_visit_bidirectional_binary_tree/512           6036 ns       6036 ns     115226  
BM_depth_first_visit_bidirectional_binary_tree/4096         48781 ns      48782 ns      14235  
BM_depth_first_visit_bidirectional_binary_tree/32768       403355 ns     403362 ns       1763  
BM_depth_first_visit_bidirectional_binary_tree/262144     3540310 ns    3540360 ns        189  
BM_depth_first_visit_bidirectional_binary_tree/524288     6697171 ns    6697186 ns        105  
BM_depth_first_visit_adjacency_list/8                         239 ns        239 ns    2928178  
BM_depth_first_visit_adjacency_list/64                       1000 ns       1000 ns     786335  
BM_depth_first_visit_adjacency_list/512                      6204 ns       6204 ns     111296  
BM_depth_first_visit_adjacency_list/4096                    48338 ns      48339 ns      14968  
BM_depth_first_visit_adjacency_list/32768                  396744 ns     396750 ns       1811  
BM_depth_first_visit_adjacency_list/262144                5808978 ns    5809037 ns         92  
BM_depth_first_visit_adjacency_list/524288                9923609 ns    9923620 ns         64  
*** Finished ***  
```

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2018-12-08 00:26:02 UTC  
> Url: https://github.com/boostorg/graph/pull/139#issuecomment-445411303  

I don't get why this isomorphism test is failing on this branch:  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/graph/test/isomorphism.test/gcc-7.3.0/debug/visibility-hidden/isomorphism.o  
In file included from isomorphism.cpp:24:0:  
isomorphism.cpp: In function ‘void test_isomorphism2()’:  
isomorphism.cpp:136:76: error: no matching function for call to ‘isomorphism(graph1&, graph2&, boost::lazy_enable_if_c<true, boost::parameter::aux::tag<boost::graph::keywords::tag::isomorphism_map, boost::associative_property_map<std::map<long unsigned int, void*> > > >::type)’  
                (g1, g2, _isomorphism_map = make_assoc_property_map(mapping)));  
                                                                            ^  
```

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2018-12-08 10:49:07 UTC  
> Url: https://github.com/boostorg/graph/pull/139#issuecomment-445450041  

> I don't get why this isomorphism test is failing on this branch:  
  
Ah, I see, it's not me. This compilation error is fixed by #137 .

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2018-12-10 12:23:34 UTC  
> Url: https://github.com/boostorg/graph/pull/139#issuecomment-445797499  

Hmmm, I can't get GCC 4.6 to install on my machine, so I'm going to have some trouble tracking down why it's not compiling for that version. Hmmm, unless, I guess Boost.Config might shed some light in the feature macros?

---

## Comment 5

> Username: anadon  
> Created_at: 2019-04-09 16:47:37 UTC  
> Url: https://github.com/boostorg/graph/pull/139#issuecomment-481331354  

@jeremy-murphy Ping

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2019-04-12 02:01:37 UTC  
> Url: https://github.com/boostorg/graph/pull/139#issuecomment-482407030  

Yup, still working on it, any help appreciated.

---

## Comment 7

> Username: jiayuehua  
> Created_at: 2020-09-24 03:37:53 UTC  
> Url: https://github.com/boostorg/graph/pull/139#issuecomment-698091684  

I think the tranverse algorithm may be implemented use coroutine and shoule be more efficient than use stack.

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2023-05-07 23:32:46 UTC  
> Updated_at: 2023-05-07 23:33:09 UTC  
> Url: https://github.com/boostorg/graph/pull/139#issuecomment-1537567650  

I'm planning to put some effort into this PR again, so I'll try to make some priorities:  
1. Resolve conflicts with `develop`.  
2. Change implementation to use `std::optional<Node>` instead of the complicated free list business.  
3. Support properties.  
4. Support `k > 2`.  
  
Anything else?

---
