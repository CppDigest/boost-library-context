# #333 - ctest failed on Mac OS with Intel Iris Graphics Card only [Open]

> Username: ethanjyx  
> Created at: 2014-12-06 20:42:49 UTC  
> Updated at: 2017-03-21 04:14:53 UTC  
> Url: https://github.com/boostorg/compute/issues/333  

1. ctest -R core.device -V  
  
UpdateCTestConfiguration  from :/Users/yixing/Desktop/compute/build/DartConfiguration.tcl  
UpdateCTestConfiguration  from :/Users/yixing/Desktop/compute/build/DartConfiguration.tcl  
Test project /Users/yixing/Desktop/compute/build  
Constructing a list of tests  
Done constructing a list of tests  
Checking test dependency graph...  
Checking test dependency graph end  
test 5  
    Start 5: core.device  
  
5: Test command: /Users/yixing/Desktop/compute/build/test/test_device  
5: Test timeout computed to be: 9.99988e+06  
5: Running 10 test cases...  
5: /Users/yixing/Desktop/compute/test/test_device.cpp:98: error in "partition_device_equally": check device.is_subdevice() == false failed  
5: skipping test: device does not support CL_DEVICE_PARTITION_EQUALLY  
5: skipping test: device does not support CL_DEVICE_PARTITION_BY_COUNTS  
5: /Users/yixing/Desktop/compute/test/test_device.cpp:197: error in "partition_by_affinity_domain": check device.is_subdevice() == false failed  
5: skipping test: device does not support partitioning by affinity domain  
5: Iris  
5:   
5: **\* 2 failures detected in test suite "TestDevice"  
1/1 Test #5: core.device ......................***Failed    0.03 sec  
  
0% tests passed, 1 tests failed out of 1  
  
Total Test time (real) =   0.04 sec  
  
The following tests FAILED:  
      5 - core.device (Failed)  
Errors while running CTest  
1. ctest -R core.event -V  
  
UpdateCTestConfiguration  from :/Users/yixing/Desktop/compute/build/DartConfiguration.tcl  
UpdateCTestConfiguration  from :/Users/yixing/Desktop/compute/build/DartConfiguration.tcl  
Test project /Users/yixing/Desktop/compute/build  
Constructing a list of tests  
Done constructing a list of tests  
Checking test dependency graph...  
Checking test dependency graph end  
test 6  
    Start 6: core.event  
  
6: Test command: /Users/yixing/Desktop/compute/build/test/test_event  
6: Test timeout computed to be: 9.99988e+06  
6: Running 2 test cases...  
6: /Users/yixing/Desktop/compute/test/test_event.cpp:49: error in "event_callback": check callback_invoked == true failed [true != true]  
6:   
6: **\* 1 failure detected in test suite "TestEvent"  
1/1 Test #6: core.event .......................***Failed    0.04 sec  
  
0% tests passed, 1 tests failed out of 1  
  
Total Test time (real) =   0.05 sec  
  
The following tests FAILED:  
      6 - core.event (Failed)  
Errors while running CTest  
1. ctest -R core.program -V  
  
UpdateCTestConfiguration  from :/Users/yixing/Desktop/compute/build/DartConfiguration.tcl  
UpdateCTestConfiguration  from :/Users/yixing/Desktop/compute/build/DartConfiguration.tcl  
Test project /Users/yixing/Desktop/compute/build  
Constructing a list of tests  
Done constructing a list of tests  
Checking test dependency graph...  
Checking test dependency graph end  
test 14  
    Start 14: core.program  
  
14: Test command: /Users/yixing/Desktop/compute/build/test/test_program  
14: Test timeout computed to be: 9.99988e+06  
14: Running 7 test cases...  
14: /Users/yixing/Desktop/compute/include/boost/compute/context.hpp:258: fatal error in "static void boost::compute::context::default_error_handler(const char _, const void *, size_t, void *)": std::exception: [CL_INVALID_KERNEL_NAME] : OpenCL Error : Failed: Could not find kernel "baz" in program 0x7fc572c26a60.  
14: /Users/yixing/Desktop/compute/test/test_program.cpp:68: last checkpoint  
14: /Users/yixing/Desktop/compute/include/boost/compute/context.hpp:258: fatal error in "static void boost::compute::context::default_error_handler(const char *, const void *, size_t, void *)": std::exception: OpenCL Build Warning : Compiler build log:  
14: <program source>:1:3: warning: no previous prototype for function 'square'  
14: T square(T x) { return x \* x; }  
14:   ^  
14:   
14:   
14: /Users/yixing/Desktop/compute/test/test_program.cpp:100: last checkpoint  
14: /Users/yixing/Desktop/compute/include/boost/compute/context.hpp:258: fatal error in "static void boost::compute::context::default_error_handler(const char *, const void *, size_t, void *)": std::exception: [CL_DEVICE_NOT_AVAILABLE] : OpenCL Error : Error: Build Program driver returned (10015)  
14: /Users/yixing/Desktop/compute/test/test_program.cpp:100: last checkpoint  
14:   
14: *_\* 3 failures detected in test suite "TestProgram"  
1/1 Test #14: core.program .....................***Failed    0.08 sec  
  
0% tests passed, 1 tests failed out of 1  
  
Total Test time (real) =   0.08 sec  
  
The following tests FAILED:  
     14 - core.program (Failed)  
Errors while running CTest  
  
There are more...  
##

---

## Comment 1

> Username: youngtaekoh  
> Created at: 2014-12-20 22:42:28 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-67752673  

Tests also fail on OSX with ATI Radeon HD 6750M (2011 MBP '17 early)  
  
> $ make test  
> The following tests FAILED:  
>       5 - core.device (Failed)  
>       7 - core.function (Failed)  
>      14 - core.program (Failed)  
>      74 - algorithm.stable_sort (Failed)  
>      79 - algorithm.lexicographical_compare (Failed)  
>      84 - container.dynamic_bitset (Failed)  
>     111 - random.bernoulli_distribution (Failed)  
>     112 - random.discrete_distribution (Failed)  
>     131 - issue.user_defined_types (Failed)  
>     134 - example.black_scholes (Failed)  
>     138 - example.host_sort (SEGFAULT)  
>     153 - example.simple_moving_average (OTHER_FAULT)  
> Errors while running CTest  
  
Followings are outputs for some test cases.  
  
> $ ./test/test_device   
> Running 11 test cases...  
> /opt/local/src/compute/test/test_device.cpp:104: error in "partition_device_equally": check device.is_subdevice() == false failed  
> skipping test: device does not support CL_DEVICE_PARTITION_EQUALLY  
> skipping test: device does not support CL_DEVICE_PARTITION_BY_COUNTS  
> /opt/local/src/compute/test/test_device.cpp:203: error in "partition_by_affinity_domain": check device.is_subdevice() == false failed  
> skipping test: device does not support partitioning by affinity domain  
> ATI Radeon HD 6750M  
>   
> $ ./test/test_program  
> Running 7 test cases...  
> /opt/local/src/compute/include/boost/compute/context.hpp:258: fatal error in "static void boost::compute::context::default_error_handler(const char *, const void *, size_t, void *)": std::exception: [CL_INVALID_KERNEL_NAME] : OpenCL Error : Failed: Could not find kernel "baz" in program 0x7fbac3e06130.  
> /opt/local/src/compute/test/test_program.cpp:68: last checkpoint  
> /opt/local/src/compute/include/boost/compute/context.hpp:258: fatal error in "static void boost::compute::context::default_error_handler(const char *, const void *, size_t, void *)": std::exception: OpenCL Build Warning : Compiler build log:  
> <program source>:3:3: warning: no previous prototype for function 'square'  
> T square(T x) { return x \* x; }  
>   ^  
> /opt/local/src/compute/test/test_program.cpp:100: last checkpoint  
> /opt/local/src/compute/include/boost/compute/context.hpp:258: fatal error in "static void boost::compute::context::default_error_handler(const char *, const void *, size_t, void *)": std::exception: [CL_DEVICE_NOT_AVAILABLE] : OpenCL Error : Error: Build Program driver returned (-2)  
> /opt/local/src/compute/test/test_program.cpp:100: last checkpoint  
>   
> **\* 3 failures detected in test suite "TestProgram"

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-12-21 01:23:27 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-67756609  

Thanks for reporting these. I'll look into it.

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-12-23 06:42:44 UTC  
> Updated at: 2014-12-24 20:01:39 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-67925964  

These should be fixed by PR #354. Could you guys try that out?

---

## Comment 4

> Username: youngtaekoh  
> Created at: 2014-12-24 21:51:20 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-68075726  

Commit fa2fd2 gives me compile errors.  
I'm using OS X Yosemite with Xcode 6.1.1 (clang-600.0.56)  
  
> [  5%] Building CXX object test/CMakeFiles/test_bernoulli_distribution.dir/test_bernoulli_distribution.cpp.o  
> In file included from /opt/local/src/compute/test/test_bernoulli_distribution.cpp:17:  
> In file included from /opt/local/src/compute/include/boost/compute/random/default_random_engine.hpp:14:  
> In file included from /opt/local/src/compute/include/boost/compute/random/mersenne_twister_engine.hpp:20:  
> In file included from /opt/local/src/compute/include/boost/compute/algorithm/transform.hpp:17:  
> /opt/local/src/compute/include/boost/compute/iterator/transform_iterator.hpp:228:51: error:   
>       'base_type' is a private member of  
>       'boost::iterators::detail::iterator_facade_baseboost::compute::buffer_iterator<bool,  
>       bool, std::__1::random_access_iterator_tag,  
>       boost::compute::detail::buffer_value<bool>, long, true, true>'  
>             transform_iterator<typename Iterator::base_type,  
>                                                   ^  
> /opt/local/src/compute/include/boost/compute/algorithm/copy.hpp:68:19: note:   
>       during template argument deduction for class template partial  
>       specialization 'is_device_iterator<type-parameter-0-0, typename  
>       enable_if<is_same<transform_iterator<typename  
>       type-parameter-0-0::base_type, typename  
>       type-parameter-0-0::unary_function>, typename  
>       remove_const<type-parameter-0-0>::type>, void>::type>' [with Iterator =  
>       boost::compute::buffer_iterator<bool>]  
>                   is_device_iterator<OutputIterator>::value  
>                   ^  
> /opt/local/src/compute/include/boost/compute/algorithm/copy.hpp:344:12: note:   
>       while substituting deduced template arguments into function template  
>       'dispatch_copy' [with InputIterator =  
>       boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
>       int>, boost::compute::function<bool (unsigned int)> >, OutputIterator =  
>       boost::compute::buffer_iterator<bool>]  
>     return detail::dispatch_copy(first, last, result, queue);  
>            ^  
> /opt/local/src/compute/include/boost/compute/algorithm/transform.hpp:40:12: note:   
>       in instantiation of function template specialization  
>       'boost::compute::copy<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
>       int>, boost::compute::function<bool (unsigned int)> >,  
>       boost::compute::buffer_iterator<bool> >' requested here  
>     return copy(  
>            ^  
> /opt/local/src/compute/include/boost/compute/random/bernoulli_distribution.hpp:80:9: note:   
>       in instantiation of function template specialization  
>       'boost::compute::transform<boost::compute::buffer_iterator<unsigned int>,  
>       boost::compute::buffer_iterator<bool>, boost::compute::function<bool  
>       (unsigned int)> >' requested here  
>         transform(  
>         ^  
> /opt/local/src/compute/test/test_bernoulli_distribution.cpp:35:14: note: in  
>       instantiation of function template specialization  
>       'boost::compute::bernoulli_distribution<float>::generateboost::compute::buffer_iterator<bool,  
>       boost::compute::mersenne_twister_engine<unsigned int> >' requested here  
> distribution.generate(vec.begin(), vec.end(), engine, queue);  
>              ^  
> 1 error generated.

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-12-24 21:58:38 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-68075962  

Hmm, interesting. What version of Boost are you using? This worked for me on OS X with Boost version `1.56`.

---

## Comment 6

> Username: youngtaekoh  
> Created at: 2014-12-24 22:00:04 UTC  
> Updated at: 2014-12-24 22:09:33 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-68076003  

I'm using boost 1.57. It seems like that iterator_facade.hpp is changed in boost 1.57.

---

## Comment 7

> Username: youngtaekoh  
> Created at: 2014-12-24 22:50:29 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-68077489  

[This patch](https://gist.github.com/youngtaekoh/c13d0eb225c3b0a65765) fixes the compile problem with boost 1.57. However it causes graphics problem when executing container.mapped_view.  
core.function, algorithm.search_n, and algorithm.stable_sort couldn't pass tests.

---

## Comment 8

> Username: kylelutz  
> Created at: 2014-12-25 19:24:00 UTC  
> Updated at: 2014-12-28 17:15:45 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-68111115  

Thanks for the patch! Could you submit it as a pull-request?  
  
It does seem strange that we now have to provide the `base_type` typedef ourselves. Any idea of what actually changed in `iterator_facade`? I'll try to look into this more.

---

## Comment 9

> Username: kylelutz  
> Created at: 2014-12-30 02:43:42 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-68325357  

@youngtaekoh Hmm, I just updated my Mac to Boost 1.57 but still cannot reproduce this. But anyway, I have a new patch which removes the use of `base_type` in `transform_iterator`, see PR #393. Could you try that out and let me know if it fixes this issue for you?

---

## Comment 10

> Username: kylelutz  
> Created at: 2014-12-30 22:07:50 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-68403918  

@youngtaekoh That change is now in `develop`, could you test that out on your system?

---

## Comment 11

> Username: youngtaekoh  
> Created at: 2014-12-30 23:55:09 UTC  
> Updated at: 2014-12-31 00:04:32 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-68412041  

@kylelutz Now it compiles without problem with boost 1.57, and the graphics problem does not occur when testing. However, 4 tests out of 156 still fails.  
  
> Total Test time (real) =  52.85 sec  
> The following tests FAILED:  
>       7 - core.function (Failed)  
>      66 - algorithm.search_n (Failed)  
>      74 - algorithm.stable_sort (Failed)  
>     133 - misc.user_defined_types (Failed)  
> Errors while running CTest  
> make: **\* [test] Error 8  
  
However, when running test_search_n separately, it shows no error as follows  
  
> $ ./test_search_n   
> Running 2 test cases...  
> **\* No errors detected  
  
Test results of others are as follows  
  
> ./test_function  
> Running 7 test cases...  
> /opt/local/src/compute/test/test_function.cpp:90: error in "sort_pairs": check data[0] == std::make_pair(0, 4.2f) failed  
> /opt/local/src/compute/test/test_function.cpp:91: error in "sort_pairs": check data[1] == std::make_pair(1, 2.3f) failed  
> /opt/local/src/compute/test/test_function.cpp:92: error in "sort_pairs": check data[2] == std::make_pair(2, 1.0f) failed  
> /opt/local/src/compute/test/test_function.cpp:102: error in "sort_pairs": check data[0] == std::make_pair(2, 1.0f) failed  
> /opt/local/src/compute/test/test_function.cpp:103: error in "sort_pairs": check data[1] == std::make_pair(1, 2.3f) failed  
> /opt/local/src/compute/test/test_function.cpp:104: error in "sort_pairs": check data[2] == std::make_pair(0, 4.2f) failed  
> **\* 6 failures detected in test suite "TestFunction"  
>   
> $ ./test_stable_sort   
> Running 2 test cases...  
> /opt/local/src/compute/test/test_stable_sort.cpp:59: error in "sort_int2": check compute::is_sorted(vec.begin(), vec.end(), compare_first, queue) == false failed  
> **\* 1 failure detected in test suite "TestStableSort"  
>   
> $ ./test_user_defined_types   
> Running 1 test case...  
> /opt/local/src/compute/test/test_user_defined_types.cpp:118: error in "issue_11": check tmp[i] == host_vector[i] failed [(9, 40, 50) != (3, 27, 22.3077)]  
> /opt/local/src/compute/test/test_user_defined_types.cpp:118: error in "issue_11": check tmp[i] == host_vector[i] failed [(44, 78, 17.6923) != (7, 49, 56.1538)]  
> /opt/local/src/compute/test/test_user_defined_types.cpp:118: error in "issue_11": check tmp[i] == host_vector[i] failed [(58, 30, 55.3846) != (9, 40, 50)]  
> /opt/local/src/compute/test/test_user_defined_types.cpp:118: error in "issue_11": check tmp[i] == host_vector[i] failed [(7, 49, 56.1538) != (40, 12, 2.30769)]  
> /opt/local/src/compute/test/test_user_defined_types.cpp:118: error in "issue_11": check tmp[i] == host_vector[i] failed [(92, 42, 66.9231) != (44, 78, 17.6923)]  
> /opt/local/src/compute/test/test_user_defined_types.cpp:118: error in "issue_11": check tmp[i] == host_vector[i] failed [(40, 12, 2.30769) != (58, 30, 55.3846)]  
> /opt/local/src/compute/test/test_user_defined_types.cpp:118: error in "issue_11": check tmp[i] == host_vector[i] failed [(3, 27, 22.3077) != (60, 33, 76.1538)]  
> /opt/local/src/compute/test/test_user_defined_types.cpp:118: error in "issue_11": check tmp[i] == host_vector[i] failed [(60, 33, 76.1538) != (78, 16, 26.9231)]  
> /opt/local/src/compute/test/test_user_defined_types.cpp:118: error in "issue_11": check tmp[i] == host_vector[i] failed [(78, 16, 26.9231) != (92, 42, 66.9231)]  
> **\* 9 failures detected in test suite "TestUserDefinedTypes"

---

## Comment 12

> Username: kylelutz  
> Created at: 2014-12-31 00:07:25 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-68412713  

Glad it's compiling again. I'll look into the test failures more. What device are you using?

---

## Comment 13

> Username: youngtaekoh  
> Created at: 2014-12-31 00:10:23 UTC  
> Updated at: 2014-12-31 00:10:28 UTC  
> Url: https://github.com/boostorg/compute/issues/333#issuecomment-68412879  

I'm using MacBook Pro (17-inch, 2011 early) with ATI Radeon HD 6750M  
  
> $./opencl_test   
> platform 0 has 2 devices:  
>   device: Intel(R) Core(TM) i7-2720QM CPU @ 2.20GHz  
>   device: ATI Radeon HD 6750M
