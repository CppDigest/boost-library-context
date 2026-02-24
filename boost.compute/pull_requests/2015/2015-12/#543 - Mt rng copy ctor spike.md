# #543 Mt rng copy ctor spike [Closed]

> Username: okdshin  
> Created at: 2015-12-25 17:53:07 UTC  
> Updated at: 2015-12-26 10:26:31 UTC  
> Closed at: 2015-12-26 08:30:23 UTC  
> Url: https://github.com/boostorg/compute/pull/543  

The copy ctor and assign op of compute::mersenne_twister_engine does not copy its states. So copied engine causes "Invalid Program" error.  
This modification solves this problem.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2015-12-25 19:00:57 UTC  
> Url: https://github.com/boostorg/compute/pull/543#issuecomment-167258774  

Thanks! I think you should rebase your branch on the latest master revision.  
25 gru 2015 18:53 "s.okada" notifications@github.com napisał(a):  
  
> The copy ctor and assign op of compute::mersenne_twister_engine does not  
> copy its states. So copied engine causes "Invalid Program" error.  
>   
> ## This modification solves this problem.  
>   
> You can view, comment on, or merge this pull request online at:  
>   
>   https://github.com/boostorg/compute/pull/543  
> Commit Summary  
> - Add missing queue and context parameters in tests  
> - Add top-level index.html file  
> - Reduce by key algorithm (serial implementation)  
> - Parallel reduce by key algorithm implementation  
> - Reduce_by_key benchmarks for Boost.Compute, Thrust and BOLT  
> - Fix violations of Boost min/max guidelines  
> - Convert tabs to spaces  
> - Add missing Boost license info and copyrights  
> - Update Boost macros, macros style unification  
> - Add more tests for sort  
> - Merge sort for CPU devices  
> - Using merge with merge path algorithm in merge sort  
> - Fix overlooked violation of the Boost min/max guidelines  
> - Using serial merge in merge algorithm for small inputs  
> - CPU-targeted merge sort adapted for sorting by key  
> - Using serial_reduce() when reducing on CPU device  
> - Checking multiple values per thread in find_if_with_atomics()  
> - enqueue_\* methods in command_queue class now return event object  
> - Add missing enqueue_map_image method in command queue  
> - Fix perf_max_element benchmark  
> - Test if min/max_element algorithms return the first min/max in given  
>   range  
> - Add invoke() utility function  
> - Support for custom comparision function in find_extrema() and  
>   min/max_element()  
> - Rename find_extrema_reduce() to find_extrema_with_reduce()  
> - fix a few instances where the device type is not used as a bit mask  
> - Add reduce by key algorithm to algorithm.hpp meta-header  
> - Add asynchronous versions of a few methods in command_queue class  
> - enqueue_map_buffer returns associated event through parameter  
> - Fix for search and search_n algorithm  
> - Add test for zip_iterator with constant_iterator  
> - Remove redundant kernel compilation from binary_find  
> - Fix test for binary_search, lower_bound and upper_bound  
> - Fix binary_find algorithm  
> - Add array support to BOOST_COMPUTE_ADAPT_STRUCT()  
> - Make event::wait() const  
> - Make wait_list::wait() const  
> - Remove extra semicolon in perf_bolt_saxpy.cpp  
> - Runtime OpenCL version check in tests  
> - Fix find_extrema_with_reduce  
> - scatter if operation  
> - Copyrights fixes  
> - fix for initialization of output vector  
> - Skip fill() tests with double if not supported by device  
> - Fix find_extrema_with_atomics  
> - Add test for vector move ctor when custom allocator is used  
> - Add more test for vector with custom allocator  
> - Fix vector when used with custom allocator  
> - Remove Jamroot file  
> - Fix Boost.Test link error in test/Jamfile.v2  
> - Fix OpenCL2.0 test cases in test_fill and test_svm_ptr  
> - Skipping copy_svm_ptr test case on AMD devices  
> - Add more tests for enqueue_svm_memcpy() method  
> - CL_MAP_WRITE_INVALIDATE_REGION is available since OpenCL 1.2  
> - Fix integer type to uint_ (cl_uint)  
> - if the ifstream can't be created in program::create_with_source_file  
>   then it throws std::ios_base::failure.  
> - tested the wrong function.  
> - Add program::create_with_source() with vector of strings  
> - Minor fixes related to Boost Inspection Report (1.60 develop)  
> - Fix incorrect count (global work size) update in generate methods  
> - Fix seed method call in linear_congruential_engine.hpp  
> - Release v0.5  
> - Modify mersenne_twister_engine to copy its states.  
>   
> File Changes  
> - _M_ CMakeLists.txt  
>   https://github.com/boostorg/compute/pull/543/files#diff-0 (9)  
> - _D_ Jamroot  
>   https://github.com/boostorg/compute/pull/543/files#diff-1 (1)  
> - _M_ doc/Jamfile.v2  
>   https://github.com/boostorg/compute/pull/543/files#diff-2 (9)  
> - _M_ doc/advanced_topics.qbk  
>   https://github.com/boostorg/compute/pull/543/files#diff-3 (8)  
> - _M_ doc/compute.qbk  
>   https://github.com/boostorg/compute/pull/543/files#diff-4 (8)  
> - _M_ doc/design.qbk  
>   https://github.com/boostorg/compute/pull/543/files#diff-5 (8)  
> - _M_ doc/faq.qbk  
>   https://github.com/boostorg/compute/pull/543/files#diff-6 (8)  
> - _M_ doc/getting_started.qbk  
>   https://github.com/boostorg/compute/pull/543/files#diff-7 (8)  
> - _M_ doc/interop.qbk  
>   https://github.com/boostorg/compute/pull/543/files#diff-8 (8)  
> - _M_ doc/introduction.qbk  
>   https://github.com/boostorg/compute/pull/543/files#diff-9 (8)  
> - _M_ doc/performance.qbk  
>   https://github.com/boostorg/compute/pull/543/files#diff-10 (8)  
> - _M_ doc/platforms_and_compilers.qbk  
>   https://github.com/boostorg/compute/pull/543/files#diff-11 (8)  
> - _M_ doc/porting_guide.qbk  
>   https://github.com/boostorg/compute/pull/543/files#diff-12 (8)  
> - _M_ doc/reference.qbk  
>   https://github.com/boostorg/compute/pull/543/files#diff-13 (8)  
> - _M_ doc/tutorial.qbk  
>   https://github.com/boostorg/compute/pull/543/files#diff-14 (8)  
> - _M_ example/CMakeLists.txt  
>   https://github.com/boostorg/compute/pull/543/files#diff-15 (9)  
> - _M_ example/opencv_convolution.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-16 (4)  
> - _M_ example/opencv_optical_flow.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-17 (4)  
> - _M_ example/qimage_blur.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-18 (10)  
> - _M_ example/simple_moving_average.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-19 (4)  
> - _M_ include/boost/compute/algorithm.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-20 (1)  
> - _M_ include/boost/compute/algorithm/accumulate.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-21 (4)  
> - _M_ include/boost/compute/algorithm/detail/binary_find.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-22 (68)  
> - _M_ include/boost/compute/algorithm/detail/find_extrema.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-23 (21)  
> - _D_ include/boost/compute/algorithm/detail/find_extrema_reduce.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-24 (260)  
> - _M_  
>   include/boost/compute/algorithm/detail/find_extrema_with_atomics.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-25 (45)  
> - _A_  
>   include/boost/compute/algorithm/detail/find_extrema_with_reduce.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-26 (441)  
> - _M_ include/boost/compute/algorithm/detail/find_if_with_atomics.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-27 (158)  
> - _A_ include/boost/compute/algorithm/detail/merge_sort_on_cpu.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-28 (366)  
> - _M_ include/boost/compute/algorithm/detail/merge_with_merge_path.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-29 (51)  
> - _A_ include/boost/compute/algorithm/detail/reduce_by_key.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-30 (119)  
> - _A_  
>   include/boost/compute/algorithm/detail/reduce_by_key_with_scan.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-31 (519)  
> - _M_ include/boost/compute/algorithm/detail/serial_find_extrema.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-32 (21)  
> - _A_ include/boost/compute/algorithm/detail/serial_reduce_by_key.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-33 (108)  
> - _M_ include/boost/compute/algorithm/max_element.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-34 (39)  
> - _M_ include/boost/compute/algorithm/merge.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-35 (34)  
> - _M_ include/boost/compute/algorithm/min_element.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-36 (41)  
> - _M_ include/boost/compute/algorithm/minmax_element.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-37 (25)  
> - _M_ include/boost/compute/algorithm/reduce.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-38 (18)  
> - _A_ include/boost/compute/algorithm/reduce_by_key.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-39 (118)  
> - _A_ include/boost/compute/algorithm/scatter_if.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-40 (119)  
> - _M_ include/boost/compute/algorithm/search.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-41 (15)  
> - _M_ include/boost/compute/algorithm/search_n.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-42 (17)  
> - _M_ include/boost/compute/algorithm/sort.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-43 (45)  
> - _M_ include/boost/compute/algorithm/sort_by_key.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-44 (93)  
> - _M_ include/boost/compute/algorithm/stable_sort.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-45 (34)  
> - _M_ include/boost/compute/async/future.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-46 (4)  
> - _M_ include/boost/compute/async/wait.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-47 (8)  
> - _M_ include/boost/compute/command_queue.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-48 (474)  
> - _M_ include/boost/compute/config.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-49 (27)  
> - _M_ include/boost/compute/container/vector.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-50 (45)  
> - _M_ include/boost/compute/detail/duration.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-51 (6)  
> - _M_ include/boost/compute/event.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-52 (2)  
> - _M_ include/boost/compute/functional/bind.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-53 (4)  
> - _M_ include/boost/compute/kernel.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-54 (4)  
> - _M_ include/boost/compute/program.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-55 (28)  
> - _M_ include/boost/compute/random/linear_congruential_engine.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-56 (6)  
> - _M_ include/boost/compute/random/mersenne_twister_engine.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-57 (9)  
> - _M_ include/boost/compute/random/normal_distribution.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-58 (4)  
> - _M_ include/boost/compute/random/threefry_engine.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-59 (75)  
> - _M_ include/boost/compute/random/uniform_int_distribution.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-60 (2)  
> - _M_ include/boost/compute/system.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-61 (8)  
> - _M_ include/boost/compute/types/struct.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-62 (9)  
> - _M_ include/boost/compute/types/tuple.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-63 (10)  
> - _M_ include/boost/compute/utility.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-64 (1)  
> - _M_ include/boost/compute/utility/dim.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-65 (4)  
> - _M_ include/boost/compute/utility/extents.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-66 (8)  
> - _A_ include/boost/compute/utility/invoke.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-67 (71)  
> - _M_ include/boost/compute/utility/wait_list.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-68 (2)  
> - _M_ include/boost/compute/version.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-69 (4)  
> - _A_ index.html  
>   https://github.com/boostorg/compute/pull/543/files#diff-70 (20)  
> - _M_ perf/CMakeLists.txt  
>   https://github.com/boostorg/compute/pull/543/files#diff-71 (12)  
> - _M_ perf/perf.py  
>   https://github.com/boostorg/compute/pull/543/files#diff-72 (9)  
> - _M_ perf/perf_accumulate.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-73 (2)  
> - _M_ perf/perf_binary_find.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-74 (4)  
> - _M_ perf/perf_bolt_max_element.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-75 (19)  
> - _A_ perf/perf_bolt_reduce_by_key.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-76 (100)  
> - _M_ perf/perf_bolt_saxpy.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-77 (2)  
> - _M_ perf/perf_exclusive_scan.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-78 (10)  
> - _M_ perf/perf_max_element.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-79 (34)  
> - _M_ perf/perf_merge.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-80 (2)  
> - _A_ perf/perf_reduce_by_key.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-81 (114)  
> - _M_ perf/perf_saxpy.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-82 (2)  
> - _M_ perf/perf_sort.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-83 (2)  
> - _M_ perf/perf_stl_merge.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-84 (4)  
> - _M_ perf/perf_tbb_merge.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-85 (12)  
> - _M_ perf/perf_thrust_exclusive_scan.cu  
>   https://github.com/boostorg/compute/pull/543/files#diff-86 (2)  
> - _A_ perf/perf_thrust_reduce_by_key.cu  
>   https://github.com/boostorg/compute/pull/543/files#diff-87 (91)  
> - _M_ perf/perfdoc.py  
>   https://github.com/boostorg/compute/pull/543/files#diff-88 (7)  
> - _M_ test/CMakeLists.txt  
>   https://github.com/boostorg/compute/pull/543/files#diff-89 (12)  
> - _M_ test/Jamfile.v2  
>   https://github.com/boostorg/compute/pull/543/files#diff-90 (10)  
> - _M_ test/context_setup.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-91 (10)  
> - _M_ test/extra/CMakeLists.txt  
>   https://github.com/boostorg/compute/pull/543/files#diff-92 (9)  
> - _M_ test/opencl_version_check.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-93 (10)  
> - _M_ test/quirks.hpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-94 (11)  
> - _M_ test/test_accumulate.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-95 (17)  
> - _M_ test/test_any_all_none_of.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-96 (27)  
> - _M_ test/test_array.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-97 (4)  
> - _M_ test/test_async_wait.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-98 (4)  
> - _M_ test/test_binary_search.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-99 (91)  
> - _M_ test/test_buffer.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-100 (2)  
> - _M_ test/test_closure.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-101 (6)  
> - _M_ test/test_command_queue.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-102 (20)  
> - _M_ test/test_complex.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-103 (67)  
> - _M_ test/test_context.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-104 (6)  
> - _M_ test/test_copy.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-105 (56)  
> - _M_ test/test_copy_if.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-106 (46)  
> - _M_ test/test_count.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-107 (64)  
> - _M_ test/test_equal.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-108 (19)  
> - _M_ test/test_equal_range.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-109 (16)  
> - _M_ test/test_extrema.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-110 (88)  
> - _M_ test/test_fill.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-111 (74)  
> - _M_ test/test_find.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-112 (23)  
> - _M_ test/test_flat_map.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-113 (22)  
> - _M_ test/test_flat_set.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-114 (35)  
> - _M_ test/test_generate.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-115 (6)  
> - _M_ test/test_image1d.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-116 (2)  
> - _M_ test/test_image2d.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-117 (62)  
> - _A_ test/test_invoke.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-118 (59)  
> - _M_ test/test_iota.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-119 (25)  
> - _M_ test/test_is_permutation.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-120 (2)  
> - _M_ test/test_kernel.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-121 (4)  
> - _M_ test/test_lambda.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-122 (51)  
> - _M_ test/test_lexicographical_compare.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-123 (17)  
> - _M_ test/test_malloc.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-124 (2)  
> - _M_ test/test_mersenne_twister_engine.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-125 (54)  
> - _M_ test/test_mismatch.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-126 (7)  
> - _M_ test/test_next_permutation.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-127 (4)  
> - _M_ test/test_pair.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-128 (90)  
> - _M_ test/test_partial_sum.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-129 (8)  
> - _M_ test/test_partition.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-130 (20)  
> - _M_ test/test_pinned_allocator.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-131 (4)  
> - _M_ test/test_pipe.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-132 (2)  
> - _M_ test/test_prev_permutation.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-133 (4)  
> - _M_ test/test_program.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-134 (25)  
> - _M_ test/test_program_cache.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-135 (6)  
> - _M_ test/test_random_fill.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-136 (8)  
> - _M_ test/test_random_shuffle.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-137 (8)  
> - _A_ test/test_reduce_by_key.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-138 (210)  
> - _M_ test/test_replace.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-139 (14)  
> - _M_ test/test_reverse.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-140 (39)  
> - _A_ test/test_scatter_if.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-141 (140)  
> - _M_ test/test_search.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-142 (19)  
> - _M_ test/test_search_n.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-143 (15)  
> - _M_ test/test_sort.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-144 (168)  
> - _M_ test/test_struct.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-145 (38)  
> - _M_ test/test_svm_ptr.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-146 (56)  
> - _M_ test/test_tuple.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-147 (38)  
> - _M_ test/test_unique_copy.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-148 (6)  
> - _M_ test/test_user_defined_types.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-149 (12)  
> - _M_ test/test_user_event.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-150 (2)  
> - _M_ test/test_vector.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-151 (204)  
> - _M_ test/test_zip_iterator.cpp  
>   https://github.com/boostorg/compute/pull/543/files#diff-152 (30)  
>   
> Patch Links:  
> - https://github.com/boostorg/compute/pull/543.patch  
> - https://github.com/boostorg/compute/pull/543.diff  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/compute/pull/543.

---

## Comment 2

> Username: okdshin  
> Created_at: 2015-12-26 08:30:23 UTC  
> Url: https://github.com/boostorg/compute/pull/543#issuecomment-167294798  

Sorry. I'll retry.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2015-12-26 08:41:48 UTC  
> Url: https://github.com/boostorg/compute/pull/543#issuecomment-167295166  

You can rebase and just force push your branch. You don't need to close  
this pull request.  
26 gru 2015 09:30 "s.okada" notifications@github.com napisał(a):  
  
> Sorry. I'll retry.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/compute/pull/543#issuecomment-167294798.

---

## Comment 4

> Username: okdshin  
> Created_at: 2015-12-26 10:26:31 UTC  
> Url: https://github.com/boostorg/compute/pull/543#issuecomment-167310369  

OK. I'll not close next time. Sorry.  
I fixed the branch and resent pull request.

---
