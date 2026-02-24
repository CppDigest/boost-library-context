# #2 - Tests failing [Closed]

> Username: ddemidov  
> Created at: 2013-03-19 20:44:33 UTC  
> Updated at: 2014-11-02 14:24:55 UTC  
> Closed at: 2013-05-24 05:25:09 UTC  
> Url: https://github.com/boostorg/compute/issues/2  

Hello Kyle,  
  
Here is output of `make test` on NVIDIA and AMD cards:  
  
Nvidia Tesla K20:  
  
```  
Test project /home/demidov/work/opencl/compute/build  
      Start  1: core.buffer  
 1/69 Test  #1: core.buffer ............................   Passed    0.53 sec  
      Start  2: core.command_queue  
 2/69 Test  #2: core.command_queue .....................   Passed    0.88 sec  
      Start  3: core.device  
 3/69 Test  #3: core.device ............................   Passed    0.12 sec  
      Start  4: core.image2d  
 4/69 Test  #4: core.image2d ...........................   Passed    0.51 sec  
      Start  5: core.image3d  
 5/69 Test  #5: core.image3d ...........................   Passed    0.51 sec  
      Start  6: core.image_sampler  
 6/69 Test  #6: core.image_sampler .....................   Passed    0.51 sec  
      Start  7: core.kernel  
 7/69 Test  #7: core.kernel ............................***Failed    0.50 sec  
      Start  8: core.program  
 8/69 Test  #8: core.program ...........................   Passed    0.49 sec  
      Start  9: core.system  
 9/69 Test  #9: core.system ............................   Passed    0.12 sec  
      Start 10: core.type_traits  
10/69 Test #10: core.type_traits .......................   Passed    0.01 sec  
      Start 11: core.types  
11/69 Test #11: core.types .............................   Passed    0.00 sec  
      Start 12: algorithm.accumulate  
12/69 Test #12: algorithm.accumulate ...................***Failed    0.51 sec  
      Start 13: algorithm.adjacent_difference  
13/69 Test #13: algorithm.adjacent_difference ..........   Passed    0.50 sec  
      Start 14: algorithm.adjacent_find  
14/69 Test #14: algorithm.adjacent_find ................   Passed    0.50 sec  
      Start 15: algorithm.any_all_none_of  
15/69 Test #15: algorithm.any_all_none_of ..............   Passed    0.51 sec  
      Start 16: algorithm.binary_search  
16/69 Test #16: algorithm.binary_search ................   Passed    0.51 sec  
      Start 17: algorithm.copy  
17/69 Test #17: algorithm.copy .........................***Failed    0.51 sec  
      Start 18: algorithm.copy_if  
18/69 Test #18: algorithm.copy_if ......................   Passed    0.50 sec  
      Start 19: algorithm.count  
19/69 Test #19: algorithm.count ........................***Failed    0.51 sec  
      Start 20: algorithm.equal  
20/69 Test #20: algorithm.equal ........................   Passed    0.50 sec  
      Start 21: algorithm.equal_range  
21/69 Test #21: algorithm.equal_range ..................   Passed    0.53 sec  
      Start 22: algorithm.extrema  
22/69 Test #22: algorithm.extrema ......................   Passed    0.49 sec  
      Start 23: algorithm.fill  
23/69 Test #23: algorithm.fill .........................   Passed    0.51 sec  
      Start 24: algorithm.find  
24/69 Test #24: algorithm.find .........................   Passed    0.50 sec  
      Start 25: algorithm.for_each  
25/69 Test #25: algorithm.for_each .....................   Passed    0.50 sec  
      Start 26: algorithm.gather  
26/69 Test #26: algorithm.gather .......................   Passed    0.50 sec  
      Start 27: algorithm.generate  
27/69 Test #27: algorithm.generate .....................   Passed    0.49 sec  
      Start 28: algorithm.histogram  
28/69 Test #28: algorithm.histogram ....................   Passed    0.51 sec  
      Start 29: algorithm.inner_product  
29/69 Test #29: algorithm.inner_product ................   Passed    0.51 sec  
      Start 30: algorithm.inplace_reduce  
30/69 Test #30: algorithm.inplace_reduce ...............***Failed    2.26 sec  
      Start 31: algorithm.insertion_sort  
31/69 Test #31: algorithm.insertion_sort ...............***Failed    1.69 sec  
      Start 32: algorithm.iota  
32/69 Test #32: algorithm.iota .........................   Passed    2.89 sec  
      Start 33: algorithm.is_sorted  
33/69 Test #33: algorithm.is_sorted ....................   Passed    2.76 sec  
      Start 34: algorithm.merge  
34/69 Test #34: algorithm.merge ........................   Passed    1.71 sec  
      Start 35: algorithm.mismatch  
35/69 Test #35: algorithm.mismatch .....................   Passed    0.51 sec  
      Start 36: algorithm.partial_sum  
36/69 Test #36: algorithm.partial_sum ..................   Passed    0.51 sec  
      Start 37: algorithm.partition  
37/69 Test #37: algorithm.partition ....................***Failed    6.62 sec  
      Start 38: algorithm.radix_sort  
38/69 Test #38: algorithm.radix_sort ...................***Failed    2.57 sec  
      Start 39: algorithm.random_shuffle  
39/69 Test #39: algorithm.random_shuffle ...............   Passed    1.04 sec  
      Start 40: algorithm.reduce  
40/69 Test #40: algorithm.reduce .......................***Failed    1.68 sec  
      Start 41: algorithm.remove  
41/69 Test #41: algorithm.remove .......................   Passed    6.25 sec  
      Start 42: algorithm.replace  
42/69 Test #42: algorithm.replace ......................   Passed    1.08 sec  
      Start 43: algorithm.reverse  
43/69 Test #43: algorithm.reverse ......................   Passed    3.36 sec  
      Start 44: algorithm.scan  
44/69 Test #44: algorithm.scan .........................***Failed    0.51 sec  
      Start 45: algorithm.scatter  
45/69 Test #45: algorithm.scatter ......................***Failed    1.11 sec  
      Start 46: algorithm.sort  
46/69 Test #46: algorithm.sort .........................***Failed   11.38 sec  
      Start 47: algorithm.stable_sort  
47/69 Test #47: algorithm.stable_sort ..................   Passed    0.50 sec  
      Start 48: algorithm.transform  
48/69 Test #48: algorithm.transform ....................***Failed    4.70 sec  
      Start 49: algorithm.transform_reduce  
49/69 Test #49: algorithm.transform_reduce .............***Failed    1.13 sec  
      Start 50: container.allocator  
50/69 Test #50: container.allocator ....................   Passed    0.50 sec  
      Start 51: container.array  
51/69 Test #51: container.array ........................***Failed    0.51 sec  
      Start 52: container.flat_map  
52/69 Test #52: container.flat_map .....................   Passed   19.66 sec  
      Start 53: container.flat_set  
53/69 Test #53: container.flat_set .....................***Failed    1.70 sec  
      Start 54: container.stack  
54/69 Test #54: container.stack ........................   Passed    1.63 sec  
      Start 55: container.string  
55/69 Test #55: container.string .......................   Passed    0.50 sec  
      Start 56: container.valarray  
56/69 Test #56: container.valarray .....................   Passed    0.51 sec  
      Start 57: container.vector  
57/69 Test #57: container.vector .......................   Passed    7.94 sec  
      Start 58: iterator.adjacent_transform_iterator  
58/69 Test #58: iterator.adjacent_transform_iterator ...   Passed    1.07 sec  
      Start 59: iterator.zip_iterator  
59/69 Test #59: iterator.zip_iterator ..................   Passed    1.46 sec  
      Start 60: random.mersenne_twister  
60/69 Test #60: random.mersenne_twister ................   Passed    1.15 sec  
      Start 61: blas.gemm  
61/69 Test #61: blas.gemm ..............................***Failed    1.67 sec  
      Start 62: blas.gemv  
62/69 Test #62: blas.gemv ..............................   Passed    1.07 sec  
      Start 63: blas.iamax  
63/69 Test #63: blas.iamax .............................   Passed    1.08 sec  
      Start 64: blas.norm2  
64/69 Test #64: blas.norm2 .............................   Passed    1.62 sec  
      Start 65: ext.complex  
65/69 Test #65: ext.complex ............................   Passed    3.91 sec  
      Start 66: ext.lambda  
66/69 Test #66: ext.lambda .............................   Passed    1.65 sec  
      Start 67: ext.malloc  
67/69 Test #67: ext.malloc .............................   Passed    0.51 sec  
      Start 68: ext.pair  
68/69 Test #68: ext.pair ...............................   Passed    2.82 sec  
      Start 69: ext.tuple  
69/69 Test #69: ext.tuple ..............................***Failed    0.51 sec  
  
74% tests passed, 18 tests failed out of 69  
  
Total Test time (real) = 119.04 sec  
  
The following tests FAILED:  
      7 - core.kernel (Failed)  
     12 - algorithm.accumulate (Failed)  
     17 - algorithm.copy (Failed)  
     19 - algorithm.count (Failed)  
     30 - algorithm.inplace_reduce (Failed)  
     31 - algorithm.insertion_sort (Failed)  
     37 - algorithm.partition (Failed)  
     38 - algorithm.radix_sort (Failed)  
     40 - algorithm.reduce (Failed)  
     44 - algorithm.scan (Failed)  
     45 - algorithm.scatter (Failed)  
     46 - algorithm.sort (Failed)  
     48 - algorithm.transform (Failed)  
     49 - algorithm.transform_reduce (Failed)  
     51 - container.array (Failed)  
     53 - container.flat_set (Failed)  
     61 - blas.gemm (Failed)  
     69 - ext.tuple (Failed)  
```  
  
AMD Capeverde:  
  
```  
Test project /home/demidov/work/opencl/compute/build/test  
      Start  1: core.buffer  
 1/69 Test  #1: core.buffer ............................   Passed    2.12 sec  
      Start  2: core.command_queue  
 2/69 Test  #2: core.command_queue .....................   Passed    0.89 sec  
      Start  3: core.device  
 3/69 Test  #3: core.device ............................   Passed    0.29 sec  
      Start  4: core.image2d  
 4/69 Test  #4: core.image2d ...........................***Failed    0.88 sec  
      Start  5: core.image3d  
 5/69 Test  #5: core.image3d ...........................   Passed    0.29 sec  
      Start  6: core.image_sampler  
 6/69 Test  #6: core.image_sampler .....................   Passed    0.29 sec  
      Start  7: core.kernel  
 7/69 Test  #7: core.kernel ............................***Failed    0.29 sec  
      Start  8: core.program  
 8/69 Test  #8: core.program ...........................***Failed    0.29 sec  
      Start  9: core.system  
 9/69 Test  #9: core.system ............................   Passed    0.29 sec  
      Start 10: core.type_traits  
10/69 Test #10: core.type_traits .......................   Passed    0.01 sec  
      Start 11: core.types  
11/69 Test #11: core.types .............................   Passed    0.00 sec  
      Start 12: algorithm.accumulate  
12/69 Test #12: algorithm.accumulate ...................***Failed    2.64 sec  
      Start 13: algorithm.adjacent_difference  
13/69 Test #13: algorithm.adjacent_difference ..........***Failed    0.60 sec  
      Start 14: algorithm.adjacent_find  
14/69 Test #14: algorithm.adjacent_find ................***Failed    0.90 sec  
      Start 15: algorithm.any_all_none_of  
15/69 Test #15: algorithm.any_all_none_of ..............***Failed    0.90 sec  
      Start 16: algorithm.binary_search  
16/69 Test #16: algorithm.binary_search ................***Failed    1.48 sec  
      Start 17: algorithm.copy  
17/69 Test #17: algorithm.copy .........................***Failed   13.78 sec  
      Start 18: algorithm.copy_if  
18/69 Test #18: algorithm.copy_if ......................***Failed    2.04 sec  
      Start 19: algorithm.count  
19/69 Test #19: algorithm.count ........................***Failed    2.34 sec  
      Start 20: algorithm.equal  
20/69 Test #20: algorithm.equal ........................***Failed    2.52 sec  
      Start 21: algorithm.equal_range  
21/69 Test #21: algorithm.equal_range ..................***Failed    0.88 sec  
      Start 22: algorithm.extrema  
22/69 Test #22: algorithm.extrema ......................***Failed    2.36 sec  
      Start 23: algorithm.fill  
23/69 Test #23: algorithm.fill .........................***Failed    1.18 sec  
      Start 24: algorithm.find  
24/69 Test #24: algorithm.find .........................***Failed    2.95 sec  
      Start 25: algorithm.for_each  
25/69 Test #25: algorithm.for_each .....................***Failed    0.91 sec  
      Start 26: algorithm.gather  
26/69 Test #26: algorithm.gather .......................***Failed    1.49 sec  
      Start 27: algorithm.generate  
27/69 Test #27: algorithm.generate .....................***Failed    0.60 sec  
      Start 28: algorithm.histogram  
28/69 Test #28: algorithm.histogram ....................***Failed    0.89 sec  
      Start 29: algorithm.inner_product  
29/69 Test #29: algorithm.inner_product ................***Failed    1.18 sec  
      Start 30: algorithm.inplace_reduce  
30/69 Test #30: algorithm.inplace_reduce ...............***Failed    1.77 sec  
      Start 31: algorithm.insertion_sort  
31/69 Test #31: algorithm.insertion_sort ...............***Failed    6.14 sec  
      Start 32: algorithm.iota  
32/69 Test #32: algorithm.iota .........................***Failed    0.88 sec  
      Start 33: algorithm.is_sorted  
33/69 Test #33: algorithm.is_sorted ....................***Failed    2.04 sec  
      Start 34: algorithm.merge  
34/69 Test #34: algorithm.merge ........................***Failed    1.18 sec  
      Start 35: algorithm.mismatch  
35/69 Test #35: algorithm.mismatch .....................***Failed    1.19 sec  
      Start 36: algorithm.partial_sum  
36/69 Test #36: algorithm.partial_sum ..................***Failed    0.88 sec  
      Start 37: algorithm.partition  
37/69 Test #37: algorithm.partition ....................***Failed    2.66 sec  
      Start 38: algorithm.radix_sort  
38/69 Test #38: algorithm.radix_sort ...................***Failed    6.13 sec  
      Start 39: algorithm.random_shuffle  
39/69 Test #39: algorithm.random_shuffle ...............***Failed    1.79 sec  
      Start 40: algorithm.reduce  
40/69 Test #40: algorithm.reduce .......................***Failed    3.51 sec  
      Start 41: algorithm.remove  
41/69 Test #41: algorithm.remove .......................***Failed    0.90 sec  
      Start 42: algorithm.replace  
42/69 Test #42: algorithm.replace ......................***Failed    0.89 sec  
      Start 43: algorithm.reverse  
43/69 Test #43: algorithm.reverse ......................***Failed    1.18 sec  
      Start 44: algorithm.scan  
44/69 Test #44: algorithm.scan .........................***Failed    3.23 sec  
      Start 45: algorithm.scatter  
45/69 Test #45: algorithm.scatter ......................***Failed    1.76 sec  
      Start 46: algorithm.sort  
46/69 Test #46: algorithm.sort .........................***Failed    9.35 sec  
      Start 47: algorithm.stable_sort  
47/69 Test #47: algorithm.stable_sort ..................***Failed    0.90 sec  
      Start 48: algorithm.transform  
48/69 Test #48: algorithm.transform ....................***Failed   12.02 sec  
      Start 49: algorithm.transform_reduce  
49/69 Test #49: algorithm.transform_reduce .............***Failed    1.52 sec  
      Start 50: container.allocator  
50/69 Test #50: container.allocator ....................   Passed    0.59 sec  
      Start 51: container.array  
51/69 Test #51: container.array ........................***Failed    6.25 sec  
      Start 52: container.flat_map  
52/69 Test #52: container.flat_map .....................***Failed    5.27 sec  
      Start 53: container.flat_set  
53/69 Test #53: container.flat_set .....................***Failed    3.85 sec  
      Start 54: container.stack  
54/69 Test #54: container.stack ........................***Failed    2.64 sec  
      Start 55: container.string  
55/69 Test #55: container.string .......................   Passed    0.60 sec  
      Start 56: container.valarray  
56/69 Test #56: container.valarray .....................***Failed    3.52 sec  
      Start 57: container.vector  
57/69 Test #57: container.vector .......................***Failed   54.50 sec  
      Start 58: iterator.adjacent_transform_iterator  
58/69 Test #58: iterator.adjacent_transform_iterator ...***Failed    1.48 sec  
      Start 59: iterator.zip_iterator  
59/69 Test #59: iterator.zip_iterator ..................***Failed    1.49 sec  
      Start 60: random.mersenne_twister  
60/69 Test #60: random.mersenne_twister ................***Failed    0.60 sec  
      Start 61: blas.gemm  
61/69 Test #61: blas.gemm ..............................***Failed    0.90 sec  
      Start 62: blas.gemv  
62/69 Test #62: blas.gemv ..............................***Failed    0.61 sec  
      Start 63: blas.iamax  
63/69 Test #63: blas.iamax .............................***Failed    0.61 sec  
      Start 64: blas.norm2  
64/69 Test #64: blas.norm2 .............................***Failed    0.60 sec  
      Start 65: ext.complex  
65/69 Test #65: ext.complex ............................***Failed    4.40 sec  
      Start 66: ext.lambda  
66/69 Test #66: ext.lambda .............................***Failed    3.24 sec  
      Start 67: ext.malloc  
67/69 Test #67: ext.malloc .............................   Passed    0.88 sec  
      Start 68: ext.pair  
68/69 Test #68: ext.pair ...............................***Failed    5.90 sec  
      Start 69: ext.tuple  
69/69 Test #69: ext.tuple ..............................   Passed    2.37 sec  
  
17% tests passed, 57 tests failed out of 69  
  
Total Test time (real) = 206.29 sec  
  
The following tests FAILED:  
      4 - core.image2d (Failed)  
      7 - core.kernel (Failed)  
      8 - core.program (Failed)  
     12 - algorithm.accumulate (Failed)  
     13 - algorithm.adjacent_difference (Failed)  
     14 - algorithm.adjacent_find (Failed)  
     15 - algorithm.any_all_none_of (Failed)  
     16 - algorithm.binary_search (Failed)  
     17 - algorithm.copy (Failed)  
     18 - algorithm.copy_if (Failed)  
     19 - algorithm.count (Failed)  
     20 - algorithm.equal (Failed)  
     21 - algorithm.equal_range (Failed)  
     22 - algorithm.extrema (Failed)  
     23 - algorithm.fill (Failed)  
     24 - algorithm.find (Failed)  
     25 - algorithm.for_each (Failed)  
     26 - algorithm.gather (Failed)  
     27 - algorithm.generate (Failed)  
     28 - algorithm.histogram (Failed)  
     29 - algorithm.inner_product (Failed)  
     30 - algorithm.inplace_reduce (Failed)  
     31 - algorithm.insertion_sort (Failed)  
     32 - algorithm.iota (Failed)  
     33 - algorithm.is_sorted (Failed)  
     34 - algorithm.merge (Failed)  
     35 - algorithm.mismatch (Failed)  
     36 - algorithm.partial_sum (Failed)  
     37 - algorithm.partition (Failed)  
     38 - algorithm.radix_sort (Failed)  
     39 - algorithm.random_shuffle (Failed)  
     40 - algorithm.reduce (Failed)  
     41 - algorithm.remove (Failed)  
     42 - algorithm.replace (Failed)  
     43 - algorithm.reverse (Failed)  
     44 - algorithm.scan (Failed)  
     45 - algorithm.scatter (Failed)  
     46 - algorithm.sort (Failed)  
     47 - algorithm.stable_sort (Failed)  
     48 - algorithm.transform (Failed)  
     49 - algorithm.transform_reduce (Failed)  
     51 - container.array (Failed)  
     52 - container.flat_map (Failed)  
     53 - container.flat_set (Failed)  
     54 - container.stack (Failed)  
     56 - container.valarray (Failed)  
     57 - container.vector (Failed)  
     58 - iterator.adjacent_transform_iterator (Failed)  
     59 - iterator.zip_iterator (Failed)  
     60 - random.mersenne_twister (Failed)  
     61 - blas.gemm (Failed)  
     62 - blas.gemv (Failed)  
     63 - blas.iamax (Failed)  
     64 - blas.norm2 (Failed)  
     65 - ext.complex (Failed)  
     66 - ext.lambda (Failed)  
     68 - ext.pair (Failed)  
```

---

## Comment 1

> Username: ddemidov  
> Created at: 2013-03-20 05:32:18 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15159105  

I don't know what happened yesterday (I removed Intel and AMD OpenCL SDKs from my system, so probably wrong device was selected for tests), but now all of the tests pass for Tesla K20.  
  
The vexcl/boost.compute scan [example](https://github.com/ddemidov/vexcl/blob/boost.compute/examples/boost.compute.cpp) still does not work though (it outputs zeros for both inclusive and exclusive scans).

---

## Comment 2

> Username: byzhang  
> Created at: 2013-03-20 08:33:06 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15163210  

Several tests fail on CPU:  
example/list_devices  
Platform 'Intel(R) OpenCL'  
  CPU Device:        Intel(R) Core(TM) i7-3770K CPU @ 3.50GHz  
  
The following tests FAILED:  
          8 - core.kernel (Failed)  
         31 - algorithm.inplace_reduce (Failed)  
         42 - algorithm.remove (Failed)  
         58 - container.vector (Failed)  
Errors while running CTest  
make: **\* [test] Error 8  
  
Is this expected? @kylelutz

---

## Comment 3

> Username: kylelutz  
> Created at: 2013-03-20 22:17:09 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15207333  

@byzhang Not expected, all the tests should pass. Can you post the output from the failing tests?

---

## Comment 4

> Username: kylelutz  
> Created at: 2013-03-20 22:18:16 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15207386  

@ddemidov Glad to hear the tests now pass on the K20. Still having problems with scan?

---

## Comment 5

> Username: byzhang  
> Created at: 2013-03-20 22:22:00 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15207541  

I do notice some tests don't fail sometimes when I run them manually  
(instead of make test)  
  
$ test/test_kernel  
Running 3 test cases...  
/home/byzhang/src/compute/test/test_kernel.cpp(80): error in  
"get_work_group_info": check local_memory_size >= ulong_(16 *  
sizeof(float)) failed  
  
**\* 1 failure detected in test suite "TestKernel"  
  
$ test/test_inplace_reduce  
Running 3 test cases...  
/home/byzhang/src/compute/test/test_inplace_reduce.cpp(43): error in  
"sum_int": check int(vector[0]) == int(36) failed [68 != 36]  
/home/byzhang/src/compute/test/test_inplace_reduce.cpp(69): error in  
"multiply_int": check int(vector[0]) == int(72900) failed [1771470000 !=  
72900]  
  
**\* 2 failures detected in test suite "TestInplaceReduce"  
  
$ test/test_remove  
Running 1 test case...  
/home/byzhang/src/compute/test/test_remove.cpp(33): error in "remove_int":  
check vector[3] == 4 failed [3 != 4]  
/home/byzhang/src/compute/test/test_remove.cpp(45): error in "remove_int":  
check vector[4] == 5 failed [3 != 5]  
/home/byzhang/src/compute/test/test_remove.cpp(52): error in "remove_int":  
check vector[2] == 5 failed [3 != 5]  
  
**\* 3 failures detected in test suite "TestRemove"  
  
$ test/test_vector  
Running 16 test cases...  
/home/byzhang/src/compute/test/test_vector.cpp(312): error in  
"vector_erase_remove": check vector.size() == size_t(5) failed [6 != 5]  
/home/byzhang/src/compute/test/test_vector.cpp(317): error in  
"vector_erase_remove": check vector.size() == size_t(3) failed [4 != 3]  
  
**\* 2 failures detected in test suite "TestVector"  
  
Thanks,  
-B  
  
On Wed, Mar 20, 2013 at 3:17 PM, Kyle Lutz notifications@github.com wrote:  
  
> @byzhang https://github.com/byzhang Not expected, all the tests should  
> pass. Can you post the output from the failing tests?  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/kylelutz/compute/issues/2#issuecomment-15207333  
> .

---

## Comment 6

> Username: kylelutz  
> Created at: 2013-03-20 22:28:39 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15207855  

Hmm. Could you print out what `local_memory_size` is in the get_work_group_info() test-case for test_kernel?   
  
I'll have to look a little more closely at the others.

---

## Comment 7

> Username: byzhang  
> Created at: 2013-03-20 22:32:03 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15208020  

$ test/test_kernel  
Running 3 test cases...  
local_memory_size:0  
/home/byzhang/src/compute/test/test_kernel.cpp(83): error in  
"get_work_group_info": check local_memory_size >= ulong_(16 *  
sizeof(float)) failed  
  
**\* 1 failure detected in test suite "TestKernel"  
  
Thanks,  
-B  
  
On Wed, Mar 20, 2013 at 3:28 PM, Kyle Lutz notifications@github.com wrote:  
  
> Hmm. Could you print out what local_memory_size is in the  
> get_work_group_info() test-case for test_kernel?  
>   
> I'll have to look a little more closely at the others.  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/kylelutz/compute/issues/2#issuecomment-15207855  
> .

---

## Comment 8

> Username: kylelutz  
> Created at: 2013-03-20 22:34:42 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15208161  

Hmm, looks like intel doesn't actually allocate (or report) the local memory. I'll just remove that check.

---

## Comment 9

> Username: byzhang  
> Created at: 2013-03-20 23:56:11 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15211610  

Kyle, if I switch to AMD implementation, the failed test cases increase a  
lot. (Is it expected? I'm considering buying a real GPU card, need to  
choose from AMD/Nvidia)  
$ example/list_devices  
Platform 'AMD Accelerated Parallel Processing'  
  CPU Device: Intel(R) Core(TM) i7-3770K CPU @ 3.50GHz  
  
The following tests FAILED:  
          2 - core.command_queue (Failed)  
          5 - core.image2d (Failed)  
          8 - core.kernel (Failed)  
          9 - core.program (Failed)  
         13 - algorithm.accumulate (Failed)  
         14 - algorithm.adjacent_difference (Failed)  
         15 - algorithm.adjacent_find (Failed)  
         16 - algorithm.any_all_none_of (Failed)  
         17 - algorithm.binary_search (Failed)  
         18 - algorithm.copy (Failed)  
         19 - algorithm.copy_if (Failed)  
         20 - algorithm.count (Failed)  
         21 - algorithm.equal (Failed)  
         22 - algorithm.equal_range (Failed)  
         23 - algorithm.extrema (Failed)  
         24 - algorithm.fill (Failed)  
         25 - algorithm.find (Failed)  
         26 - algorithm.for_each (Failed)  
         27 - algorithm.gather (Failed)  
         28 - algorithm.generate (Failed)  
         29 - algorithm.histogram (Failed)  
         30 - algorithm.inner_product (Failed)  
         31 - algorithm.inplace_reduce (Failed)  
         32 - algorithm.insertion_sort (Failed)  
         33 - algorithm.iota (Failed)  
         34 - algorithm.is_sorted (Failed)  
         35 - algorithm.merge (Failed)  
         36 - algorithm.mismatch (Failed)  
         37 - algorithm.partial_sum (Failed)  
         38 - algorithm.partition (Failed)  
         39 - algorithm.radix_sort (Failed)  
         40 - algorithm.random_shuffle (Failed)  
         41 - algorithm.reduce (Failed)  
         42 - algorithm.remove (Failed)  
         43 - algorithm.replace (Failed)  
         44 - algorithm.reverse (Failed)  
         45 - algorithm.scan (Failed)  
         46 - algorithm.scatter (Failed)  
         47 - algorithm.sort (Failed)  
         48 - algorithm.stable_sort (Failed)  
         49 - algorithm.transform (Failed)  
         50 - algorithm.transform_reduce (Failed)  
         52 - container.array (Failed)  
         53 - container.flat_map (Failed)  
         54 - container.flat_set (Failed)  
         55 - container.stack (Failed)  
         57 - container.valarray (Failed)  
         58 - container.vector (Failed)  
         59 - iterator.adjacent_transform_iterator (Failed)  
         60 - iterator.constant_iterator (Failed)  
         61 - iterator.counting_iterator (Failed)  
         62 - iterator.permutation_iterator (Failed)  
         63 - iterator.transform_iterator (Failed)  
         64 - iterator.zip_iterator (Failed)  
         65 - random.mersenne_twister (Failed)  
         66 - blas.gemm (Failed)  
         67 - blas.gemv (Failed)  
         68 - blas.iamax (Failed)  
         69 - blas.norm2 (Failed)  
         70 - ext.complex (Failed)  
         71 - ext.lambda (Failed)  
         73 - ext.pair (Failed)  
Errors while running CTest  
make: **\* [test] Error 8  
  
Thanks,  
-B  
  
On Wed, Mar 20, 2013 at 3:34 PM, Kyle Lutz notifications@github.com wrote:  
  
> Hmm, looks like intel doesn't actually allocate (or report) the local  
> memory. I'll just remove that check.  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/kylelutz/compute/issues/2#issuecomment-15208161  
> .

---

## Comment 10

> Username: ddemidov  
> Created at: 2013-03-21 11:42:01 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15231808  

Kyle,   
scan works fine now, but I have problems with sort.  
  
[This example](https://github.com/ddemidov/vexcl/blob/master/examples/boost.compute/sort.cpp) works fine with Tesla C2075, but gives incorrect results on Tesla K20c.  
  
perf_sort_uint segfaults every other time, and perf_sort_float may either segfault, show `ERROR: is_sorted() returned false`, or finish normally. Both tests select K20 for computation. Btw, is there a way to control what device is selected for the tests to run?

---

## Comment 11

> Username: ddemidov  
> Created at: 2013-03-21 12:29:35 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15233503  

Ah, found BOOST_COMPUTE_DEFAULT_DEVICE in the code. The perf_sort_\* tests run fine on Tesla C2075, but not on K20c, or Intel CPU.

---

## Comment 12

> Username: kylelutz  
> Created at: 2013-03-22 02:22:35 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15277829  

@byzhang I was able to reproduce the failures for test_remove and test_vector with the intel SDK on the CPU and I'll get a patch pushed soon.  
  
@ddemidov I'm getting the same segfault with the Intel SDK on the CPU for perf_sort_\* and also when running perf_partial_sum with large sizes. I think there may be a bug in scan() but I'll have to look into it more.

---

## Comment 13

> Username: byzhang  
> Created at: 2013-03-22 05:59:57 UTC  
> Updated at: 2013-03-22 06:00:11 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15282487  

@kylelutz Thanks! Now only one failed test for intel OpenCL:  
$ test/test_inplace_reduce  
Running 3 test cases...  
/home/byzhang/src/compute/test/test_inplace_reduce.cpp(43): error in "sum_int": check int(vector[0]) == int(36) failed [68 != 36]  
/home/byzhang/src/compute/test/test_inplace_reduce.cpp(69): error in "multiply_int": check int(vector[0]) == int(72900) failed [1771470000 != 72900]  
  
**\* 2 failures detected in test suite "TestInplaceReduce"

---

## Comment 14

> Username: ddemidov  
> Created at: 2013-03-26 14:58:56 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15463168  

Tests status after the recent commits:  
- NVIDIA Teslas C2075 and K20c pass the tests, but perf_sort_\* do not work on K20c  
- Intel Core i7 passes all tests with Intel OpenCL  
- Intel Core i7 fails the following tests with AMD OpenCL:  
  
```  
      2 - core.command_queue (Failed)  
     32 - algorithm.insertion_sort (Failed)  
     39 - algorithm.radix_sort (Failed)  
     47 - algorithm.sort (Failed)  
```  
- AMD Radeon HD 7970 (Tahiti) and 7770 (Capeverde) fail the following tests:  
  
```  
     32 - algorithm.insertion_sort (Failed)  
     39 - algorithm.radix_sort (Failed)  
     47 - algorithm.sort (Failed)  
```  
  
Plus test 72 - ext.pair hangs on both of the GPUs (and sometimes on a CPU).

---

## Comment 15

> Username: kylelutz  
> Created at: 2013-03-27 02:43:01 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15501346  

Could you post the output of the failing `core.command_queue` and `algorithm.insertion_sort` tests?

---

## Comment 16

> Username: ddemidov  
> Created at: 2013-03-27 02:46:54 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15501419  

```  
$ BOOST_COMPUTE_DEFAULT_DEVICE=Intel ./test_command_queue   
Running 3 test cases...  
/home/demidov/work/opencl/compute/include/boost/compute/detail/get_object_info.hpp(37): fatal error in "T boost::compute::detail::_get_object_info_impl<T, Function, Object, Info>::operator()(Function, Object, Info) [with T = long unsigned int; Function = int (*)(_cl_event*, unsigned int, long unsigned int, void*, long unsigned int*); Object = _cl_event*; Info = unsigned int]": std::exception: Profiling Information Not Available  
  
*** 1 failure detected in test suite "TestCommandQueue"  
  
$ ./test_insertion_sort   
Running 10 test cases...  
Boost.Compute: kernel compilation failed (-11)  
--- source ---  
#pragma OPENCL EXTENSION cl_khr_fp16 : enable  
#pragma OPENCL EXTENSION cl_khr_fp16 : enable  
#pragma OPENCL EXTENSION cl_khr_fp16 : enable  
  
  
__kernel void find_if(__global int* index, __global ushort* _buf0)  
{  
const uint i = get_global_id(0);  
const ushort value=((_buf0[i])<=(_buf0[1+(i)]));  
if(value==0){  
    atomic_min(index,i);  
}  
  
}  
  
--- build log ---  
"/tmp/OCLngGzcH.cl", line 1: error: can't enable all OpenCL extensions or  
          unrecognized OpenCL extension  
  #pragma OPENCL EXTENSION cl_khr_fp16 : enable  
                           ^  
  
"/tmp/OCLngGzcH.cl", line 2: error: can't enable all OpenCL extensions or  
          unrecognized OpenCL extension  
  #pragma OPENCL EXTENSION cl_khr_fp16 : enable  
                           ^  
  
"/tmp/OCLngGzcH.cl", line 3: error: can't enable all OpenCL extensions or  
          unrecognized OpenCL extension  
  #pragma OPENCL EXTENSION cl_khr_fp16 : enable  
                           ^  
  
3 errors detected in the compilation of "/tmp/OCLngGzcH.cl".  
  
Internal error: clc compiler invocation failed.  
  
/home/demidov/work/opencl/compute/include/boost/compute/program.hpp(160): fatal error in "cl_int boost::compute::program::build(const string&)": std::exception: Build Program Failure  
/home/demidov/work/opencl/compute/test/test_insertion_sort.cpp(113): last checkpoint  
  
*** 1 failure detected in test suite "TestInsertionSort"  
```

---

## Comment 17

> Username: kylelutz  
> Created at: 2013-03-27 04:15:12 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15503526  

Alright, `core.command_queue`, `algorithm.sort`, and `algorithm.insertion_sort` should now be fixed. I'll look more into `algorithm.radix_sort` as well as why `ext.pair` hangs.  
  
Thanks,  
-kyle

---

## Comment 18

> Username: ddemidov  
> Created at: 2013-03-27 05:03:33 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15504562  

test_pair outputs this before it hangs:  
  
```  
$ ./test_pair   
Running 5 test cases...  
UNREACHABLE executed!  
unknown location(0): fatal error in "fill_pair_vector": signal: SIGABRT (application abort requested)  
/home/demidov/work/opencl/compute/test/test_pair.cpp(47): last checkpoint  
```

---

## Comment 19

> Username: ddemidov  
> Created at: 2013-03-27 05:14:13 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15504793  

And here is backtrace for this:  
  
```  
#0  0x00007ffff6d8f355 in raise () from /lib64/libc.so.6  
#1  0x00007ffff6d907d8 in abort () from /lib64/libc.so.6  
#2  0x00007ffff583bb74 in llvm::llvm_unreachable_internal(char const*, char const*, unsigned int) () from /usr/lib64/libamdocl64.so  
#3  0x00007ffff5804bff in llvm::EVT::getEVT(llvm::Type*, bool) () from /usr/lib64/libamdocl64.so  
#4  0x00007ffff4e8de76 in llvm::TargetLowering::getValueType(llvm::Type*, bool) const () from /usr/lib64/libamdocl64.so  
#5  0x00007ffff5028a3c in llvm::SelectionDAGBuilder::visitFPExt(llvm::User const&) () from /usr/lib64/libamdocl64.so  
#6  0x00007ffff5006ffd in llvm::SelectionDAGBuilder::visit(unsigned int, llvm::User const&) () from /usr/lib64/libamdocl64.so  
#7  0x00007ffff5007900 in llvm::SelectionDAGBuilder::getValueImpl(llvm::Value const*) () from /usr/lib64/libamdocl64.so  
#8  0x00007ffff500d193 in llvm::SelectionDAGBuilder::getValue(llvm::Value const*) () from /usr/lib64/libamdocl64.so  
#9  0x00007ffff5023401 in llvm::SelectionDAGBuilder::visitStore(llvm::StoreInst const&) () from /usr/lib64/libamdocl64.so  
#10 0x00007ffff50070cd in llvm::SelectionDAGBuilder::visit(unsigned int, llvm::User const&) () from /usr/lib64/libamdocl64.so  
#11 0x00007ffff5032152 in llvm::SelectionDAGBuilder::visit(llvm::Instruction const&) () from /usr/lib64/libamdocl64.so  
#12 0x00007ffff50663e8 in llvm::SelectionDAGISel::SelectBasicBlock(llvm::ilist_iterator<llvm::Instruction const>, llvm::ilist_iterator<llvm::Instruction const>, bool&) ()  
   from /usr/lib64/libamdocl64.so  
#13 0x00007ffff506bd6d in llvm::SelectionDAGISel::SelectAllBasicBlocks(llvm::Function const&) () from /usr/lib64/libamdocl64.so  
#14 0x00007ffff506cd75 in llvm::SelectionDAGISel::runOnMachineFunction(llvm::MachineFunction&) () from /usr/lib64/libamdocl64.so  
#15 0x00007ffff57f2b19 in llvm::FPPassManager::runOnFunction(llvm::Function&) () from /usr/lib64/libamdocl64.so  
#16 0x00007ffff57f2c55 in llvm::FunctionPassManagerImpl::run(llvm::Function&) () from /usr/lib64/libamdocl64.so  
#17 0x00007ffff57f2e56 in llvm::FunctionPassManager::run(llvm::Function&) () from /usr/lib64/libamdocl64.so  
#18 0x00007ffff4445d54 in amd::CompilerImpl::llvmCodeGen(llvm::Module*, stlp_std::basic_string<char, stlp_std::char_traits<char>, stlp_std::allocator<char> >&, amd::CompilerTargetInfo&, llvm::JunkJITBinary**) () from /usr/lib64/libamdocl64.so  
#19 0x00007ffff444887a in amd::CompilerImpl::llvmLinkOptCG(stlp_std::basic_string<char, stlp_std::char_traits<char>, stlp_std::allocator<char> >&, stlp_std::basic_string<char, stlp_std::char_traits<char>, stlp_std::allocator<char> >&, amd::CompilerTargetInfo&, llvm::JunkJITBinary**) () from /usr/lib64/libamdocl64.so  
#20 0x00007ffff4494250 in gpu::NullProgram::compileBinaryToIL(amd::option::Options*) () from /usr/lib64/libamdocl64.so  
#21 0x00007ffff44b64b8 in gpu::NullProgram::linkImpl(amd::option::Options*) () from /usr/lib64/libamdocl64.so  
#22 0x00007ffff4450c45 in device::Program::build(stlp_std::basic_string<char, stlp_std::char_traits<char>, stlp_std::allocator<char> > const&, char const*, amd::option::Options*)  
    () from /usr/lib64/libamdocl64.so  
#23 0x00007ffff4460ce0 in amd::Program::build(stlp_std::vector<amd::Device*, stlp_std::allocator<amd::Device*> > const&, char const*, void (*)(_cl_program*, void*), void*, bool)  
    () from /usr/lib64/libamdocl64.so  
#24 0x00007ffff443cef3 in clBuildProgram () from /usr/lib64/libamdocl64.so  
#25 0x000000000041cfcc in boost::compute::program::build(std::string const&) ()  
#26 0x000000000041d123 in boost::compute::kernel::create_with_source(std::string const&, std::string const&, boost::compute::context const&) ()  
#27 0x000000000041df4d in boost::compute::detail::meta_kernel::compile(boost::compute::context const&) ()  
#28 0x000000000041e1a2 in boost::compute::detail::meta_kernel::exec_1d(boost::compute::command_queue&, unsigned long, unsigned long) ()  
#29 0x0000000000421f0f in boost::compute::buffer_iterator<std::pair<int, float> > boost::compute::detail::copy_on_device<boost::compute::constant_iterator<std::pair<int, float> >, boost::compute::buffer_iterator<std::pair<int, float> > >(boost::compute::constant_iterator<std::pair<int, float> >, boost::compute::constant_iterator<std::pair<int, float> >, boost::compute::buffer_iterator<std::pair<int, float> >, boost::compute::command_queue&) ()  
#30 0x0000000000421f88 in void boost::compute::detail::dispatch_fill<boost::compute::buffer_iterator<std::pair<int, float> >, std::pair<int, float> >(boost::compute::buffer_iterator<std::pair<int, float> >, unsigned long, std::pair<int, float> const&, boost::compute::command_queue&) ()  
#31 0x000000000041552c in void boost::compute::fill<boost::compute::buffer_iterator<std::pair<int, float> >, std::pair<int, float> >(boost::compute::buffer_iterator<std::pair<int, float> >, boost::compute::buffer_iterator<std::pair<int, float> >, std::pair<int, float> const&) [clone .isra.299] ()  
#32 0x0000000000415605 in fill_pair_vector::test_method() ()  
#33 0x000000000041601e in fill_pair_vector_invoker() ()  
#34 0x0000000000419f27 in boost::unit_test::ut_detail::callback0_impl_t<boost::unit_test::ut_detail::unused, void (*)()>::invoke() ()  
#35 0x00007ffff7992011 in boost::unit_test::ut_detail::callback0_impl_t<int, boost::unit_test::(anonymous namespace)::zero_return_wrapper_t<boost::unit_test::callback0<boost::unit_test::ut_detail::unused> > >::invoke() () from /usr/lib64/libboost_unit_test_framework.so.1.52.0  
#36 0x00007ffff796fbae in boost::execution_monitor::catch_signals(boost::unit_test::callback0<int> const&) () from /usr/lib64/libboost_unit_test_framework.so.1.52.0  
#37 0x00007ffff79704ab in boost::execution_monitor::execute(boost::unit_test::callback0<int> const&) () from /usr/lib64/libboost_unit_test_framework.so.1.52.0  
#38 0x00007ffff79920f9 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::unit_test::test_case const&) ()  
   from /usr/lib64/libboost_unit_test_framework.so.1.52.0  
#39 0x00007ffff7976f78 in boost::unit_test::framework_impl::visit(boost::unit_test::test_case const&) () from /usr/lib64/libboost_unit_test_framework.so.1.52.0  
#40 0x00007ffff79b06ab in boost::unit_test::traverse_test_tree(boost::unit_test::test_suite const&, boost::unit_test::test_tree_visitor&) ()  
   from /usr/lib64/libboost_unit_test_framework.so.1.52.0  
#41 0x00007ffff7972096 in boost::unit_test::framework::run(unsigned long, bool) () from /usr/lib64/libboost_unit_test_framework.so.1.52.0  
#42 0x00007ffff798fd5f in boost::unit_test::unit_test_main(bool (*)(), int, char**) () from /usr/lib64/libboost_unit_test_framework.so.1.52.0  
#43 0x00007ffff6d7b9b5 in __libc_start_main () from /lib64/libc.so.6  
#44 0x0000000000411825 in _start ()  
```

---

## Comment 20

> Username: ddemidov  
> Created at: 2013-03-27 05:23:15 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-15504961  

If I output kernel source in meta_kernel::compile, then I get this:  
  
```  
$ ./test_pair  
Running 5 test cases...  
-----  
typedef struct {  
    int first;  
    float second;  
} _pair_int_float_t;  
  
__kernel void copy(__global _pair_int_float_t* _buf0, __global _pair_int_float_t* _buf1)  
{  
const uint i = get_global_id(0);  
_buf0[i]=_buf1[i];  
  
}  
  
-----  
typedef struct {  
    int first;  
    float second;  
} _pair_int_float_t;  
  
__kernel void copy(__global _pair_int_float_t* _buf0)  
{  
const uint i = get_global_id(0);  
_buf0[3+(i)]=(_pair_int_float_t){0, 0};  
  
}  
  
-----  
typedef struct {  
    int first;  
    float second;  
} _pair_int_float_t;  
  
__kernel void copy(__global _pair_int_float_t* _buf0)  
{  
const uint i = get_global_id(0);  
_buf0[i]=(_pair_int_float_t){4, 2};  
  
}  
  
UNREACHABLE executed!  
unknown location(0): fatal error in "fill_pair_vector": signal: SIGABRT (application abort requested)  
/home/demidov/work/opencl/compute/test/test_pair.cpp(47): last checkpoint  
-----  
typedef struct {  
    int first;  
    float second;  
} _pair_int_float_t;  
  
__kernel void copy(__global _pair_int_float_t* _buf0, __global _pair_int_float_t* _buf1)  
{  
const uint i = get_global_id(0);  
_buf0[i]=_buf1[i];  
  
}  
  
^C  
```  
  
^^^ Here it hangs

---

## Comment 21

> Username: ddemidov  
> Created at: 2013-05-05 17:39:05 UTC  
> Updated at: 2013-05-05 17:40:12 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-17455415  

An update of the tests state with commit 9a64f6b39ab833458dfdaedf7ddaa5458ad6915e:  
  
### AMD Capeverde  
  
```  
The following tests FAILED:  
      2 - core.command_queue (Failed)  
      4 - core.device (Failed)  
```  
  
```  
$ ./test/test_command_queue   
Running 5 test cases...  
/home/demidov/work/opencl/compute/test/test_command_queue.cpp(156): error in "write_buffer_rect": check output[1] == 3 failed [2 != 3]  
/home/demidov/work/opencl/compute/test/test_command_queue.cpp(157): error in "write_buffer_rect": check output[2] == 5 failed [3 != 5]  
/home/demidov/work/opencl/compute/test/test_command_queue.cpp(158): error in "write_buffer_rect": check output[3] == 7 failed [4 != 7]  
  
*** 3 failures detected in test suite "TestCommandQueue"  
  
$ ./test/test_device   
Running 8 test cases...  
/home/demidov/work/opencl/compute/include/boost/compute/device.hpp(236): fatal error in "std::vector<boost::compute::device> boost::compute::device::partition(const cl_device_partition_property*) const": std::exception: Invalid Value  
/home/demidov/work/opencl/compute/test/test_device.cpp(58): last checkpoint  
/home/demidov/work/opencl/compute/include/boost/compute/device.hpp(236): fatal error in "std::vector<boost::compute::device> boost::compute::device::partition(const cl_device_partition_property*) const": std::exception: Invalid Value  
/home/demidov/work/opencl/compute/test/test_device.cpp(58): last checkpoint  
skipping test: device does not support partitioning by affinity domain  
  
*** 2 failures detected in test suite "TestDevice"  
```  
  
### Intel CPU (AMD OpenCL implementation)  
  
```  
The following tests FAILED:  
      4 - core.device (Failed)  
```  
  
```  
$ ./test/test_device   
Running 8 test cases...  
/home/demidov/work/opencl/compute/test/test_device.cpp(115): error in "partition_by_counts": check sub_devices[0].compute_units() == size_t(2) failed [1 != 2]  
/home/demidov/work/opencl/compute/test/test_device.cpp(117): error in "partition_by_counts": check sub_devices[2].compute_units() == size_t(1) failed [2 != 1]  
  
*** 2 failures detected in test suite "TestDevice"  
```  
  
### NVIDIA Tesla C2075  
  
```  
100% tests passed, 0 tests failed out of 77  
```  
  
### NVIDIA Tesla K20c  
  
```  
100% tests passed, 0 tests failed out of 77  
```

---

## Comment 22

> Username: ddemidov  
> Created at: 2013-05-05 18:37:45 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-17456483  

But `perf_sort_float` and `perf_sort_uint` still do not work on AMD Capeverde:  
  
```  
$ BOOST_COMPUTE_DEFAULT_DEVICE=Capeverde ../perf/perf_sort_float   
size: 1000  
time: 721.414 ms  
ERROR: is_sorted() returned false  
$ BOOST_COMPUTE_DEFAULT_DEVICE=Capeverde ../perf/perf_sort_uint   
size: 1000  
time: 712.36 ms  
ERROR: is_sorted() returned false  
```

---

## Comment 23

> Username: kylelutz  
> Created at: 2013-05-07 01:25:38 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-17518326  

Thanks for running through all the tests again. Glad to hear they all pass on NVIDIA cards (and mostly pass on the others).   
  
I've ordered a 7970 and I should have some time soon to work out the AMD bugs.

---

## Comment 24

> Username: kylelutz  
> Created at: 2013-05-10 03:21:52 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-17702453  

Alright, I think I've fixed the rest of the tests. Commits:  fecbe63043a36081b1ec41e088e2c47ff1360fc7 8934bea62797cec73bda8bfe0627ed945a928267  
  
I'm fairly sure AMD's implementation of `clEnqueueWriteBufferRect()` for its GPUs is buggy. This test works fine on NVIDIA's GPUs and on Intel CPUs (with both Intel's and AMD's OpenCL implementations). I've added code to skip the test if running on an AMD GPU.  
  
Let me know if they any tests still fail on your machines.  
  
Thanks,  
Kyle

---

## Comment 25

> Username: ddemidov  
> Created at: 2013-05-10 05:02:27 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-17704419  

Hello Kyle,   
  
all tests are passing now on NVIDIA Teslas, AMD Capeverde and Intel. The only exception is ext.pair test  that hangs again on Capeverde and Intel (AMD OpenCL). The only difference from my last report is that I am running the tests remotely now (through ssh), and I was logged in locally then. This looks as another problem of AMD implementation. I'll be able to run the tests locally next Monday.  
  
Cheers,  
Denis

---

## Comment 26

> Username: ddemidov  
> Created at: 2013-05-10 05:04:01 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-17704462  

`perf_sort_{float,uint}` are still not working on Capeverde though.

---

## Comment 27

> Username: kylelutz  
> Created at: 2013-05-11 00:49:48 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-17751485  

Hmm, does `ext.pair` consistently hang? I haven't been able to reproduce it on any of my machines. Can you run `test_pair` with the `--log_level=all` option and post the output?  
  
Also, I have a new (much faster) radix sort implementation I've been working on and, once merged, the `perf_sort` tests should pass.  
  
Thanks,  
Kyle

---

## Comment 28

> Username: ddemidov  
> Created at: 2013-05-11 04:47:16 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-17754368  

It does hang every time I run it from an ssh session. Here is the output:  
  
```  
$ BOOST_COMPUTE_DEFAULT_DEVICE=Capeverde ./test_pair --log_level=all  
Running 5 test cases...  
Entering test suite "TestPair"  
Entering test suite "compute_test"  
Entering test case "vector_pair_int_float"  
/home/demidov/work/opencl/compute/test/test_pair.cpp(30): info: check vector.size() == size_t(3) passed  
/home/demidov/work/opencl/compute/test/test_pair.cpp(31): info: check vector[0] == std::make_pair(1, 1.1f) passed  
/home/demidov/work/opencl/compute/test/test_pair.cpp(32): info: check vector[1] == std::make_pair(2, 2.2f) passed  
/home/demidov/work/opencl/compute/test/test_pair.cpp(33): info: check vector[2] == std::make_pair(3, 3.3f) passed  
Leaving test case "vector_pair_int_float"; testing time: 300ms  
Entering test case "copy_pair_vector"  
/home/demidov/work/opencl/compute/test/test_pair.cpp(43): info: check input.size() == size_t(4) passed  
/home/demidov/work/opencl/compute/test/test_pair.cpp(47): info: check output[0] == std::make_pair(1, 2.0f) passed  
/home/demidov/work/opencl/compute/test/test_pair.cpp(48): info: check output[1] == std::make_pair(3, 4.0f) passed  
/home/demidov/work/opencl/compute/test/test_pair.cpp(49): info: check output[2] == std::make_pair(5, 6.0f) passed  
/home/demidov/work/opencl/compute/test/test_pair.cpp(50): info: check output[3] == std::make_pair(7, 8.0f) passed  
Leaving test case "copy_pair_vector"; testing time: 130ms  
Entering test case "fill_pair_vector"  
UNREACHABLE executed!  
unknown location(0): fatal error in "fill_pair_vector": signal: SIGABRT (application abort requested)  
Leaving test case "fill_pair_vector"; testing time: 50ms  
Entering test case "transform_pair_get"  
^C  
```  
  
Judging by stack trace I posted earlier, the problem is within AMD's OpenCL compiler. From my experience, sometimes it helps to rearrange the code a bit. For example, replace struct assignment with member-wise one.

---

## Comment 29

> Username: ddemidov  
> Created at: 2013-05-14 07:53:38 UTC  
> Updated at: 2013-05-14 07:53:50 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-17861707  

Please see https://gist.github.com/ddemidov/5574346. I've created minimal example that reproduces the issue with test_pair.

---

## Comment 30

> Username: ddemidov  
> Created at: 2013-05-14 08:29:37 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-17863160  

I've posted the bug description here: http://devgurus.amd.com/thread/166622

---

## Comment 31

> Username: kylelutz  
> Created at: 2013-05-14 11:57:37 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-17871397  

Cool, thanks! Does AMD have a public bug tracker? Or is everything through their forums?

---

## Comment 32

> Username: ddemidov  
> Created at: 2013-05-14 11:59:47 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-17871474  

If they do, I am not aware of that. I think I saw advice from their stuff to post any bug reports to the forums.

---

## Comment 33

> Username: shehzan10  
> Created at: 2014-11-01 22:25:39 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-61386074  

I'm running into the test_kernel error on NVIDIA Quadro K5000. Any suggestions on what could fix it?  
Here's the output  
  
```  
build(master)$ ./test/test_kernel                                                                                                                                     
Running 6 test cases...  
/workspace/area51/compute/include/boost/compute/program.hpp(271): fatal error in "void boost::compute::program::build(const string&)": std::exception: Build Program Failure  
/workspace/area51/compute/test/test_kernel.cpp(101): last checkpoint  
  
*** 1 failure detected in test suite "TestKernel"  
```  
  
Another failure: if I enable C++11 and run tests on the K5000, then test_event also fails. Here's the output for that.  
  
```  
build(master)$ ./test/test_event  
Running 4 test cases...  
unknown location(0): fatal error in "lambda_callback": memory access violation at address: 0x00000000: no mapping at fault address  
/workspace/area51/compute/test/test_event.cpp(27): last checkpoint  
  
*** 1 failure detected in test suite "TestEvent"  
[Exit 201 ]  
```  
  
I'm running g++ 4.8 with CUDA 6.5.

---

## Comment 34

> Username: kylelutz  
> Created at: 2014-11-02 00:35:09 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-61389376  

For `test_kernel`, could you try adding `#define BOOST_COMPUTE_DEBUG_KERNEL_COMPILATION` to the top of the file (before any `<boost/compute/*>` includes)? This will have the test print out the build log when compilation fails which should help in narrowing down the problem.  
  
As for `test_event`, we should probably add a `REQUIRES_OPENCL_VERSION(1,2);` to the top of the `lambda_callback` test function just like `event_callback` test function has.

---

## Comment 35

> Username: shehzan10  
> Created at: 2014-11-02 01:24:04 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-61390500  

Here is the debug output for kernel:  
  
```  
build(master)$ ./test/test_kernel  
Running 5 test cases...  
Boost.Compute: kernel compilation failed (-11)  
--- source ---  
__kernel void sum_kernel(__global const int *input, const uint size, __global int *result) { int sum = 0; for(uint i = 0; i < size; i++){ sum += input[i]; } *result = sum; }  
--- build log ---  
Error in processing command line: Don't understand command line argument "-cl-kernel-arg-info"!  
/workspace/area51/compute/include/boost/compute/program.hpp(271): fatal error in "void boost::compute::program::build(const string&)": std::exception: Build Program Failure  
/workspace/area51/compute/test/test_kernel.cpp(103): last checkpoint  
  
*** 1 failure detected in test suite "TestKernel"  
```

---

## Comment 36

> Username: shehzan10  
> Created at: 2014-11-02 01:46:43 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-61391014  

For the event failure,I added `REQUIRES_OPENCL_VERSION(1,2);` to null_event, lambda_callback and event_to_std_future. That makes the tests pass.

---

## Comment 37

> Username: kylelutz  
> Created at: 2014-11-02 02:57:51 UTC  
> Updated at: 2014-11-02 02:58:25 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-61392440  

Oh yeah, the `get_arg_info` test in `test_kernel` should also be marked with `REQUIRES_OPENCL_VERSION(1,2)` as the `clGetKernelArgInfo()` function was added in version 1.2.   
  
Thanks for testing these!

---

## Comment 38

> Username: shehzan10  
> Created at: 2014-11-02 14:24:55 UTC  
> Url: https://github.com/boostorg/compute/issues/2#issuecomment-61408537  

That works. I'll watch out for those fixes.
