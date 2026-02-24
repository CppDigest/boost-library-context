# #313 cmake support for building and running tests [Merged]

> Username: nigels-com  
> Created at: 2025-09-05 01:40:10 UTC  
> Updated at: 2025-09-06 10:42:46 UTC  
> Merged at: 2025-09-06 10:42:35 UTC  
> Closed at: 2025-09-06 10:42:35 UTC  
> Url: https://github.com/boostorg/container/pull/313  

Extending the cmake build to support `-DBUILD_TESTING=Y` mode.  
  
Adapted from [boostorg/sort --- test/CMakeLists.txt ](https://github.com/boostorg/sort/blob/develop/test/CMakeLists.txt)  
  
Tests build and pass on Ubuntu 24.04 (Noble) g++ v13.3.0  
  
Build:  
  
```  
$ cmake .. --fresh -G Ninja -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=Y -DBOOST_INCLUDE_LIBRARIES=container && ninja -v  
...  
```  
  
Test:  
  
```  
$ ninja test  
[0/2] Re-checking globbed directories...  
[0/1] Running tests...  
Test project /home/nigels/dev/boost/build  
      Start  1: boost_container_allocator_traits_test  
 1/69 Test  #1: boost_container_allocator_traits_test ...............   Passed    0.00 sec  
      Start  2: boost_container_alloc_basic_test  
 2/69 Test  #2: boost_container_alloc_basic_test ....................   Passed    0.00 sec  
      Start  3: boost_container_alloc_full_test  
 3/69 Test  #3: boost_container_alloc_full_test .....................   Passed    0.00 sec  
      Start  4: boost_container_boost_iterator_comp_test  
 4/69 Test  #4: boost_container_boost_iterator_comp_test ............   Passed    0.00 sec  
      Start  5: boost_container_common_iterator_test  
 5/69 Test  #5: boost_container_common_iterator_test ................   Passed    0.00 sec  
      Start  6: boost_container_copy_move_algo_test  
 6/69 Test  #6: boost_container_copy_move_algo_test .................   Passed    0.00 sec  
      Start  7: boost_container_deque_options_test  
 7/69 Test  #7: boost_container_deque_options_test ..................   Passed    0.00 sec  
      Start  8: boost_container_deque_test  
 8/69 Test  #8: boost_container_deque_test ..........................   Passed    0.01 sec  
      Start  9: boost_container_devector_options_test  
 9/69 Test  #9: boost_container_devector_options_test ...............   Passed    0.00 sec  
      Start 10: boost_container_devector_test  
10/69 Test #10: boost_container_devector_test .......................   Passed    0.00 sec  
      Start 11: boost_container_explicit_inst_deque_test  
11/69 Test #11: boost_container_explicit_inst_deque_test ............   Passed    0.00 sec  
      Start 12: boost_container_explicit_inst_devector_test  
12/69 Test #12: boost_container_explicit_inst_devector_test .........   Passed    0.00 sec  
      Start 13: boost_container_explicit_inst_flat_map_test  
13/69 Test #13: boost_container_explicit_inst_flat_map_test .........   Passed    0.00 sec  
      Start 14: boost_container_explicit_inst_flat_set_test  
14/69 Test #14: boost_container_explicit_inst_flat_set_test .........   Passed    0.00 sec  
      Start 15: boost_container_explicit_inst_list_test  
15/69 Test #15: boost_container_explicit_inst_list_test .............   Passed    0.00 sec  
      Start 16: boost_container_explicit_inst_map_test  
16/69 Test #16: boost_container_explicit_inst_map_test ..............   Passed    0.00 sec  
      Start 17: boost_container_explicit_inst_set_test  
17/69 Test #17: boost_container_explicit_inst_set_test ..............   Passed    0.00 sec  
      Start 18: boost_container_explicit_inst_slist_test  
18/69 Test #18: boost_container_explicit_inst_slist_test ............   Passed    0.00 sec  
      Start 19: boost_container_explicit_inst_small_vector_test  
19/69 Test #19: boost_container_explicit_inst_small_vector_test .....   Passed    0.00 sec  
      Start 20: boost_container_explicit_inst_stable_vector_test  
20/69 Test #20: boost_container_explicit_inst_stable_vector_test ....   Passed    0.00 sec  
      Start 21: boost_container_explicit_inst_static_vector_test  
21/69 Test #21: boost_container_explicit_inst_static_vector_test ....   Passed    0.00 sec  
      Start 22: boost_container_explicit_inst_string_test  
22/69 Test #22: boost_container_explicit_inst_string_test ...........   Passed    0.00 sec  
      Start 23: boost_container_explicit_inst_vector_test  
23/69 Test #23: boost_container_explicit_inst_vector_test ...........   Passed    0.00 sec  
      Start 24: boost_container_flat_map_adaptor_test  
24/69 Test #24: boost_container_flat_map_adaptor_test ...............   Passed    0.01 sec  
      Start 25: boost_container_flat_map_test  
25/69 Test #25: boost_container_flat_map_test .......................   Passed    0.01 sec  
      Start 26: boost_container_flat_set_adaptor_test  
26/69 Test #26: boost_container_flat_set_adaptor_test ...............   Passed    0.01 sec  
      Start 27: boost_container_flat_set_test  
27/69 Test #27: boost_container_flat_set_test .......................   Passed    0.00 sec  
      Start 28: boost_container_flat_tree_test  
28/69 Test #28: boost_container_flat_tree_test ......................   Passed    0.00 sec  
      Start 29: boost_container_global_resource_test  
29/69 Test #29: boost_container_global_resource_test ................   Passed    0.00 sec  
      Start 30: boost_container_insert_vs_emplace_test  
30/69 Test #30: boost_container_insert_vs_emplace_test ..............   Passed    0.00 sec  
      Start 31: boost_container_list_test  
31/69 Test #31: boost_container_list_test ...........................   Passed    0.02 sec  
      Start 32: boost_container_map_test  
32/69 Test #32: boost_container_map_test ............................   Passed    0.01 sec  
      Start 33: boost_container_memory_resource_test  
33/69 Test #33: boost_container_memory_resource_test ................   Passed    0.00 sec  
      Start 34: boost_container_monotonic_buffer_resource_test  
34/69 Test #34: boost_container_monotonic_buffer_resource_test ......   Passed    0.00 sec  
      Start 35: boost_container_node_handle_test  
35/69 Test #35: boost_container_node_handle_test ....................   Passed    0.00 sec  
      Start 36: boost_container_null_iterators_test  
36/69 Test #36: boost_container_null_iterators_test .................   Passed    0.00 sec  
      Start 37: boost_container_pair_test  
37/69 Test #37: boost_container_pair_test ...........................   Passed    0.00 sec  
      Start 38: boost_container_pmr_deque_test  
38/69 Test #38: boost_container_pmr_deque_test ......................   Passed    0.00 sec  
      Start 39: boost_container_pmr_devector_test  
39/69 Test #39: boost_container_pmr_devector_test ...................   Passed    0.00 sec  
      Start 40: boost_container_pmr_flat_map_test  
40/69 Test #40: boost_container_pmr_flat_map_test ...................   Passed    0.00 sec  
      Start 41: boost_container_pmr_flat_set_test  
41/69 Test #41: boost_container_pmr_flat_set_test ...................   Passed    0.00 sec  
      Start 42: boost_container_pmr_list_test  
42/69 Test #42: boost_container_pmr_list_test .......................   Passed    0.00 sec  
      Start 43: boost_container_pmr_map_test  
43/69 Test #43: boost_container_pmr_map_test ........................   Passed    0.00 sec  
      Start 44: boost_container_pmr_set_test  
44/69 Test #44: boost_container_pmr_set_test ........................   Passed    0.00 sec  
      Start 45: boost_container_pmr_slist_test  
45/69 Test #45: boost_container_pmr_slist_test ......................   Passed    0.00 sec  
      Start 46: boost_container_pmr_small_vector_test  
46/69 Test #46: boost_container_pmr_small_vector_test ...............   Passed    0.00 sec  
      Start 47: boost_container_pmr_stable_vector_test  
47/69 Test #47: boost_container_pmr_stable_vector_test ..............   Passed    0.00 sec  
      Start 48: boost_container_pmr_string_test  
48/69 Test #48: boost_container_pmr_string_test .....................   Passed    0.00 sec  
      Start 49: boost_container_pmr_vector_test  
49/69 Test #49: boost_container_pmr_vector_test .....................   Passed    0.00 sec  
      Start 50: boost_container_polymorphic_allocator_test  
50/69 Test #50: boost_container_polymorphic_allocator_test ..........   Passed    0.00 sec  
      Start 51: boost_container_resource_adaptor_test  
51/69 Test #51: boost_container_resource_adaptor_test ...............   Passed    0.00 sec  
      Start 52: boost_container_scoped_allocator_adaptor_test  
52/69 Test #52: boost_container_scoped_allocator_adaptor_test .......   Passed    0.00 sec  
      Start 53: boost_container_scoped_allocator_usage_test  
53/69 Test #53: boost_container_scoped_allocator_usage_test .........   Passed    0.00 sec  
      Start 54: boost_container_set_test  
54/69 Test #54: boost_container_set_test ............................   Passed    0.01 sec  
      Start 55: boost_container_slist_test  
55/69 Test #55: boost_container_slist_test ..........................   Passed    0.02 sec  
      Start 56: boost_container_small_vector_options_test  
56/69 Test #56: boost_container_small_vector_options_test ...........   Passed    0.00 sec  
      Start 57: boost_container_small_vector_test  
57/69 Test #57: boost_container_small_vector_test ...................   Passed    0.00 sec  
      Start 58: boost_container_stable_vector_test  
58/69 Test #58: boost_container_stable_vector_test ..................   Passed    0.01 sec  
      Start 59: boost_container_static_vector_options_test  
59/69 Test #59: boost_container_static_vector_options_test ..........   Passed    0.00 sec  
      Start 60: boost_container_static_vector_test  
60/69 Test #60: boost_container_static_vector_test ..................   Passed    0.00 sec  
      Start 61: boost_container_string_test  
61/69 Test #61: boost_container_string_test .........................   Passed    0.01 sec  
      Start 62: boost_container_string_view_compat_test  
62/69 Test #62: boost_container_string_view_compat_test .............   Passed    0.00 sec  
      Start 63: boost_container_synchronized_pool_resource_test  
63/69 Test #63: boost_container_synchronized_pool_resource_test .....   Passed    0.00 sec  
      Start 64: boost_container_throw_exception_test  
64/69 Test #64: boost_container_throw_exception_test ................   Passed    0.00 sec  
      Start 65: boost_container_tree_test  
65/69 Test #65: boost_container_tree_test ...........................   Passed    0.00 sec  
      Start 66: boost_container_unsynchronized_pool_resource_test  
66/69 Test #66: boost_container_unsynchronized_pool_resource_test ...   Passed    0.00 sec  
      Start 67: boost_container_uses_allocator_test  
67/69 Test #67: boost_container_uses_allocator_test .................   Passed    0.00 sec  
      Start 68: boost_container_vector_options_test  
68/69 Test #68: boost_container_vector_options_test .................   Passed    0.00 sec  
      Start 69: boost_container_vector_test  
69/69 Test #69: boost_container_vector_test .........................   Passed    0.00 sec  
  
100% tests passed, 0 tests failed out of 69  
  
Total Test time (real) =   0.21 sec  
```

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2025-09-06 10:42:45 UTC  
> Url: https://github.com/boostorg/container/pull/313#issuecomment-3261813644  

Many thanks!

---
