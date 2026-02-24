# #493 - test suite failing with pocl icd [Closed]

> Username: ghisvail  
> Created at: 2015-08-10 10:49:15 UTC  
> Updated at: 2016-10-14 19:48:08 UTC  
> Closed at: 2016-10-14 02:00:57 UTC  
> Url: https://github.com/boostorg/compute/issues/493  

I have tried running the test suite with the pocl OpenCL ICD which allows to run OpenCL kernels on the CPU. This is potentially useful for Linux packaging build-farms, which may not have much OpenCL compute capability to run the test suite besides on the CPU.  
  
My setup consists of an up-to-date Debian testing installation. The pocl OpenCL ICD driver is provided by the `pocl-opencl-icd` driver.  
  
The build was performed with the following CMake options `-DBOOST_COMPUTE_BUILD_TESTS=ON -DBOOST_COMPUTE_THREAD_SAFE=ON` on both the `v0.4` tagged release and the most current `develop` snapshot. Test suite was run via `make test`.  
  
Please find the respective reports in separate posts below. Feel free to ask for futher instructions and I'll try to help as much as I can.  
  
Best regards,

---

## Comment 1

> Username: ghisvail  
> Created at: 2015-08-10 10:49:44 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-129405136  

on `v0.4`  
  
```  
Running tests...  
Test project /home/ghislain/workspace/compute/build  
        Start   1: core.buffer  
  1/139 Test   #1: core.buffer ..........................***Failed    0.11 sec  
        Start   2: core.closure  
  2/139 Test   #2: core.closure .........................***Failed    0.91 sec  
        Start   3: core.command_queue  
  3/139 Test   #3: core.command_queue ...................***Failed    0.39 sec  
        Start   4: core.context  
  4/139 Test   #4: core.context .........................   Passed    0.10 sec  
        Start   5: core.device  
  5/139 Test   #5: core.device ..........................   Passed    0.10 sec  
        Start   6: core.event  
  6/139 Test   #6: core.event ...........................   Passed    0.10 sec  
        Start   7: core.function  
  7/139 Test   #7: core.function ........................   Passed    1.60 sec  
        Start   8: core.kernel  
  8/139 Test   #8: core.kernel ..........................   Passed    0.83 sec  
        Start   9: core.pipe  
  9/139 Test   #9: core.pipe ............................***Failed    0.10 sec  
        Start  10: core.platform  
 10/139 Test  #10: core.platform ........................   Passed    0.10 sec  
        Start  11: core.program  
 11/139 Test  #11: core.program .........................***Failed    0.47 sec  
        Start  12: core.system  
 12/139 Test  #12: core.system ..........................   Passed    0.10 sec  
        Start  13: core.type_traits  
 13/139 Test  #13: core.type_traits .....................   Passed    0.00 sec  
        Start  14: core.user_event  
 14/139 Test  #14: core.user_event ......................***Failed    0.10 sec  
        Start  15: utility.extents  
 15/139 Test  #15: utility.extents ......................   Passed    0.10 sec  
        Start  16: utility.program_cache  
 16/139 Test  #16: utility.program_cache ................   Passed    0.20 sec  
        Start  17: utility.wait_list  
 17/139 Test  #17: utility.wait_list ....................   Passed    0.10 sec  
        Start  18: algorithm.accumulate  
 18/139 Test  #18: algorithm.accumulate .................   Passed    2.84 sec  
        Start  19: algorithm.adjacent_difference  
 19/139 Test  #19: algorithm.adjacent_difference ........***Failed    0.52 sec  
        Start  20: algorithm.adjacent_find  
 20/139 Test  #20: algorithm.adjacent_find ..............***Failed    0.91 sec  
        Start  21: algorithm.any_all_none_of  
 21/139 Test  #21: algorithm.any_all_none_of ............   Passed    1.93 sec  
        Start  22: algorithm.binary_search  
 22/139 Test  #22: algorithm.binary_search ..............   Passed    2.35 sec  
        Start  23: algorithm.copy  
 23/139 Test  #23: algorithm.copy .......................***Failed    0.10 sec  
        Start  24: algorithm.copy_if  
 24/139 Test  #24: algorithm.copy_if ....................***Failed    0.10 sec  
        Start  25: algorithm.count  
 25/139 Test  #25: algorithm.count ......................   Passed    3.96 sec  
        Start  26: algorithm.equal  
 26/139 Test  #26: algorithm.equal ......................***Failed    0.64 sec  
        Start  27: algorithm.equal_range  
 27/139 Test  #27: algorithm.equal_range ................   Passed    2.12 sec  
        Start  28: algorithm.extrema  
 28/139 Test  #28: algorithm.extrema ....................   Passed    1.72 sec  
        Start  29: algorithm.fill  
 29/139 Test  #29: algorithm.fill .......................***Failed    0.10 sec  
        Start  30: algorithm.find  
 30/139 Test  #30: algorithm.find .......................   Passed    1.41 sec  
        Start  31: algorithm.find_end  
 31/139 Test  #31: algorithm.find_end ...................   Passed    0.69 sec  
        Start  32: algorithm.for_each  
 32/139 Test  #32: algorithm.for_each ...................   Passed    0.52 sec  
        Start  33: algorithm.gather  
 33/139 Test  #33: algorithm.gather .....................   Passed    0.93 sec  
        Start  34: algorithm.generate  
 34/139 Test  #34: algorithm.generate ...................***Failed    0.10 sec  
        Start  35: algorithm.includes  
 35/139 Test  #35: algorithm.includes ...................   Passed    0.66 sec  
        Start  36: algorithm.inner_product  
 36/139 Test  #36: algorithm.inner_product ..............   Passed    0.68 sec  
        Start  37: algorithm.inplace_merge  
 37/139 Test  #37: algorithm.inplace_merge ..............   Passed    0.39 sec  
        Start  38: algorithm.inplace_reduce  
 38/139 Test  #38: algorithm.inplace_reduce .............   Passed    0.83 sec  
        Start  39: algorithm.insertion_sort  
 39/139 Test  #39: algorithm.insertion_sort .............   Passed    2.95 sec  
        Start  40: algorithm.iota  
 40/139 Test  #40: algorithm.iota .......................***Failed    0.78 sec  
        Start  41: algorithm.is_permutation  
 41/139 Test  #41: algorithm.is_permutation .............   Passed    0.81 sec  
        Start  42: algorithm.is_sorted  
 42/139 Test  #42: algorithm.is_sorted ..................***Failed    0.67 sec  
        Start  43: algorithm.merge  
 43/139 Test  #43: algorithm.merge ......................***Failed    0.10 sec  
        Start  44: algorithm.mismatch  
 44/139 Test  #44: algorithm.mismatch ...................***Failed    0.39 sec  
        Start  45: algorithm.next_permutation  
 45/139 Test  #45: algorithm.next_permutation ...........   Passed    1.57 sec  
        Start  46: algorithm.nth_element  
 46/139 Test  #46: algorithm.nth_element ................   Passed    6.24 sec  
        Start  47: algorithm.partial_sum  
 47/139 Test  #47: algorithm.partial_sum ................   Passed    0.39 sec  
        Start  48: algorithm.partition  
 48/139 Test  #48: algorithm.partition ..................   Passed    2.10 sec  
        Start  49: algorithm.partition_point  
 49/139 Test  #49: algorithm.partition_point ............   Passed    0.39 sec  
        Start  50: algorithm.prev_permutation  
 50/139 Test  #50: algorithm.prev_permutation ...........   Passed    1.70 sec  
        Start  51: algorithm.radix_sort  
 51/139 Test  #51: algorithm.radix_sort .................   Passed    3.94 sec  
        Start  52: algorithm.random_fill  
 52/139 Test  #52: algorithm.random_fill ................   Passed    1.21 sec  
        Start  53: algorithm.random_shuffle  
 53/139 Test  #53: algorithm.random_shuffle .............   Passed    0.52 sec  
        Start  54: algorithm.reduce  
 54/139 Test  #54: algorithm.reduce .....................***Failed    0.54 sec  
        Start  55: algorithm.remove  
 55/139 Test  #55: algorithm.remove .....................   Passed    1.79 sec  
        Start  56: algorithm.replace  
 56/139 Test  #56: algorithm.replace ....................   Passed    0.53 sec  
        Start  57: algorithm.reverse  
 57/139 Test  #57: algorithm.reverse ....................   Passed    1.53 sec  
        Start  58: algorithm.rotate  
 58/139 Test  #58: algorithm.rotate .....................   Passed    0.10 sec  
        Start  59: algorithm.rotate_copy  
 59/139 Test  #59: algorithm.rotate_copy ................   Passed    0.10 sec  
        Start  60: algorithm.scan  
 60/139 Test  #60: algorithm.scan .......................***Failed    1.94 sec  
        Start  61: algorithm.scatter  
 61/139 Test  #61: algorithm.scatter ....................   Passed    0.52 sec  
        Start  62: algorithm.search  
 62/139 Test  #62: algorithm.search .....................   Passed    0.69 sec  
        Start  63: algorithm.search_n  
 63/139 Test  #63: algorithm.search_n ...................   Passed    0.74 sec  
        Start  64: algorithm.set_difference  
 64/139 Test  #64: algorithm.set_difference .............   Passed    0.93 sec  
        Start  65: algorithm.set_intersection  
 65/139 Test  #65: algorithm.set_intersection ...........   Passed    0.91 sec  
        Start  66: algorithm.set_symmetric_difference  
 66/139 Test  #66: algorithm.set_symmetric_difference ...   Passed    0.93 sec  
        Start  67: algorithm.set_union  
 67/139 Test  #67: algorithm.set_union ..................   Passed    0.92 sec  
        Start  68: algorithm.sort  
 68/139 Test  #68: algorithm.sort .......................   Passed    3.24 sec  
        Start  69: algorithm.sort_by_key  
 69/139 Test  #69: algorithm.sort_by_key ................   Passed    1.28 sec  
        Start  70: algorithm.stable_partition  
 70/139 Test  #70: algorithm.stable_partition ...........   Passed    1.05 sec  
        Start  71: algorithm.stable_sort  
 71/139 Test  #71: algorithm.stable_sort ................   Passed    1.44 sec  
        Start  72: algorithm.transform  
 72/139 Test  #72: algorithm.transform ..................   Passed    2.01 sec  
        Start  73: algorithm.transform_if  
 73/139 Test  #73: algorithm.transform_if ...............   Passed    0.78 sec  
        Start  74: algorithm.transform_reduce  
 74/139 Test  #74: algorithm.transform_reduce ...........   Passed    0.99 sec  
        Start  75: algorithm.unique  
 75/139 Test  #75: algorithm.unique .....................***Failed    0.39 sec  
        Start  76: algorithm.unique_copy  
 76/139 Test  #76: algorithm.unique_copy ................   Passed    0.39 sec  
        Start  77: algorithm.lexicographical_compare  
 77/139 Test  #77: algorithm.lexicographical_compare ....   Passed    0.69 sec  
        Start  78: allocator.buffer_allocator  
 78/139 Test  #78: allocator.buffer_allocator ...........   Passed    0.10 sec  
        Start  79: allocator.pinned_allocator  
 79/139 Test  #79: allocator.pinned_allocator ...........   Passed    0.10 sec  
        Start  80: async.wait  
 80/139 Test  #80: async.wait ...........................   Passed    0.10 sec  
        Start  81: async.wait_guard  
 81/139 Test  #81: async.wait_guard .....................***Failed    0.10 sec  
        Start  82: container.array  
 82/139 Test  #82: container.array ......................***Failed    0.10 sec  
        Start  83: container.dynamic_bitset  
 83/139 Test  #83: container.dynamic_bitset .............***Failed    0.10 sec  
        Start  84: container.flat_map  
 84/139 Test  #84: container.flat_map ...................   Passed    2.13 sec  
        Start  85: container.flat_set  
 85/139 Test  #85: container.flat_set ...................   Passed    1.59 sec  
        Start  86: container.mapped_view  
 86/139 Test  #86: container.mapped_view ................***Failed    0.10 sec  
        Start  87: container.stack  
 87/139 Test  #87: container.stack ......................   Passed    0.10 sec  
        Start  88: container.string  
 88/139 Test  #88: container.string .....................   Passed    0.82 sec  
        Start  89: container.valarray  
 89/139 Test  #89: container.valarray ...................***Failed    0.10 sec  
        Start  90: container.vector  
 90/139 Test  #90: container.vector .....................***Failed    0.39 sec  
        Start  91: exception.context_error  
 91/139 Test  #91: exception.context_error ..............   Passed    0.10 sec  
        Start  92: exception.no_device_found  
 92/139 Test  #92: exception.no_device_found ............   Passed    0.00 sec  
        Start  93: exception.opencl_error  
 93/139 Test  #93: exception.opencl_error ...............   Passed    0.10 sec  
        Start  94: exception.unsupported_extension  
 94/139 Test  #94: exception.unsupported_extension ......   Passed    0.00 sec  
        Start  95: functional.as  
 95/139 Test  #95: functional.as ........................***Failed    0.39 sec  
        Start  96: functional.bind  
 96/139 Test  #96: functional.bind ......................   Passed    1.72 sec  
        Start  97: functional.convert  
 97/139 Test  #97: functional.convert ...................   Passed    0.39 sec  
        Start  98: functional.get  
 98/139 Test  #98: functional.get .......................   Passed    0.00 sec  
        Start  99: functional.hash  
 99/139 Test  #99: functional.hash ......................   Passed    0.39 sec  
        Start 100: functional.identity  
100/139 Test #100: functional.identity ..................   Passed    0.39 sec  
        Start 101: functional.popcount  
101/139 Test #101: functional.popcount ..................   Passed    1.31 sec  
        Start 102: functional.unpack  
102/139 Test #102: functional.unpack ....................   Passed    0.66 sec  
        Start 103: image.image1d  
103/139 Test #103: image.image1d ........................***Failed    0.10 sec  
        Start 104: image.image2d  
104/139 Test #104: image.image2d ........................***Failed    0.11 sec  
        Start 105: image.image3d  
105/139 Test #105: image.image3d ........................   Passed    0.10 sec  
        Start 106: image.image_sampler  
106/139 Test #106: image.image_sampler ..................   Passed    0.10 sec  
        Start 107: iterator.buffer_iterator  
107/139 Test #107: iterator.buffer_iterator .............   Passed    0.39 sec  
        Start 108: iterator.constant_iterator  
108/139 Test #108: iterator.constant_iterator ...........   Passed    0.39 sec  
        Start 109: iterator.counting_iterator  
109/139 Test #109: iterator.counting_iterator ...........   Passed    0.39 sec  
        Start 110: iterator.discard_iterator  
110/139 Test #110: iterator.discard_iterator ............***Failed    0.10 sec  
        Start 111: iterator.function_input_iterator  
111/139 Test #111: iterator.function_input_iterator .....   Passed    0.39 sec  
        Start 112: iterator.permutation_iterator  
112/139 Test #112: iterator.permutation_iterator ........   Passed    0.39 sec  
        Start 113: iterator.strided_iterator  
113/139 Test #113: iterator.strided_iterator ............   Passed    0.66 sec  
        Start 114: iterator.transform_iterator  
114/139 Test #114: iterator.transform_iterator ..........   Passed    0.40 sec  
        Start 115: iterator.zip_iterator  
115/139 Test #115: iterator.zip_iterator ................   Passed    0.53 sec  
        Start 116: memory.local_buffer  
116/139 Test #116: memory.local_buffer ..................   Passed    0.10 sec  
        Start 117: memory.svm_ptr  
117/139 Test #117: memory.svm_ptr .......................***Failed    0.10 sec  
        Start 118: random.bernoulli_distribution  
118/139 Test #118: random.bernoulli_distribution ........   Passed    0.56 sec  
        Start 119: random.discrete_distribution  
119/139 Test #119: random.discrete_distribution .........   Passed    0.70 sec  
        Start 120: random.linear_congruential_engine  
120/139 Test #120: random.linear_congruential_engine ....   Passed    0.46 sec  
        Start 121: random.mersenne_twister_engine  
121/139 Test #121: random.mersenne_twister_engine .......   Passed    0.46 sec  
        Start 122: random.threefry_engine  
122/139 Test #122: random.threefry_engine ...............***Failed    0.49 sec  
        Start 123: random.normal_distribution  
123/139 Test #123: random.normal_distribution ...........   Passed    0.57 sec  
        Start 124: random.uniform_int_distribution  
124/139 Test #124: random.uniform_int_distribution ......   Passed    1.92 sec  
        Start 125: random.uniform_real_distribution  
125/139 Test #125: random.uniform_real_distribution .....   Passed    0.70 sec  
        Start 126: types.fundamental  
126/139 Test #126: types.fundamental ....................   Passed    0.00 sec  
        Start 127: types.complex  
127/139 Test #127: types.complex ........................***Failed    0.10 sec  
        Start 128: types.pair  
128/139 Test #128: types.pair ...........................***Failed    0.39 sec  
        Start 129: types.tuple  
129/139 Test #129: types.tuple ..........................   Passed    0.77 sec  
        Start 130: types.struct  
130/139 Test #130: types.struct .........................   Passed    0.64 sec  
        Start 131: type_traits.result_of  
131/139 Test #131: type_traits.result_of ................   Passed    0.00 sec  
        Start 132: experimental.clamp_range  
132/139 Test #132: experimental.clamp_range .............   Passed    0.39 sec  
        Start 133: experimental.malloc  
133/139 Test #133: experimental.malloc ..................   Passed    0.10 sec  
        Start 134: experimental.sort_by_transform  
134/139 Test #134: experimental.sort_by_transform .......   Passed    0.93 sec  
        Start 135: experimental.tabulate  
135/139 Test #135: experimental.tabulate ................   Passed    0.38 sec  
        Start 136: misc.amd_cpp_kernel_language  
136/139 Test #136: misc.amd_cpp_kernel_language .........   Passed    0.10 sec  
        Start 137: misc.lambda  
137/139 Test #137: misc.lambda ..........................   Passed    2.82 sec  
        Start 138: misc.user_defined_types  
138/139 Test #138: misc.user_defined_types ..............   Passed    0.40 sec  
        Start 139: misc.multiple_objects  
139/139 Test #139: misc.multiple_objects ................   Passed    0.00 sec  
  
76% tests passed, 34 tests failed out of 139  
  
Total Test time (real) = 105.71 sec  
  
The following tests FAILED:  
      1 - core.buffer (Failed)  
      2 - core.closure (Failed)  
      3 - core.command_queue (Failed)  
      9 - core.pipe (Failed)  
     11 - core.program (Failed)  
     14 - core.user_event (Failed)  
     19 - algorithm.adjacent_difference (Failed)  
     20 - algorithm.adjacent_find (Failed)  
     23 - algorithm.copy (Failed)  
     24 - algorithm.copy_if (Failed)  
     26 - algorithm.equal (Failed)  
     29 - algorithm.fill (Failed)  
     34 - algorithm.generate (Failed)  
     40 - algorithm.iota (Failed)  
     42 - algorithm.is_sorted (Failed)  
     43 - algorithm.merge (Failed)  
     44 - algorithm.mismatch (Failed)  
     54 - algorithm.reduce (Failed)  
     60 - algorithm.scan (Failed)  
     75 - algorithm.unique (Failed)  
     81 - async.wait_guard (Failed)  
     82 - container.array (Failed)  
     83 - container.dynamic_bitset (Failed)  
     86 - container.mapped_view (Failed)  
     89 - container.valarray (Failed)  
     90 - container.vector (Failed)  
     95 - functional.as (Failed)  
    103 - image.image1d (Failed)  
    104 - image.image2d (Failed)  
    110 - iterator.discard_iterator (Failed)  
    117 - memory.svm_ptr (Failed)  
    122 - random.threefry_engine (Failed)  
    127 - types.complex (Failed)  
    128 - types.pair (Failed)  
Makefile:106: recipe for target 'test' failed  
  
```

---

## Comment 2

> Username: ghisvail  
> Created at: 2015-08-10 10:49:55 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-129405163  

on `develop`:  
  
```  
Running tests...  
Test project /home/ghislain/workspace/compute/build  
        Start   1: core.buffer  
  1/140 Test   #1: core.buffer ..........................***Failed    0.12 sec  
        Start   2: core.closure  
  2/140 Test   #2: core.closure .........................***Failed    0.92 sec  
        Start   3: core.command_queue  
  3/140 Test   #3: core.command_queue ...................***Failed    0.39 sec  
        Start   4: core.context  
  4/140 Test   #4: core.context .........................   Passed    0.10 sec  
        Start   5: core.device  
  5/140 Test   #5: core.device ..........................   Passed    0.10 sec  
        Start   6: core.event  
  6/140 Test   #6: core.event ...........................   Passed    0.10 sec  
        Start   7: core.function  
  7/140 Test   #7: core.function ........................   Passed    1.60 sec  
        Start   8: core.kernel  
  8/140 Test   #8: core.kernel ..........................   Passed    0.82 sec  
        Start   9: core.pipe  
  9/140 Test   #9: core.pipe ............................***Failed    0.10 sec  
        Start  10: core.platform  
 10/140 Test  #10: core.platform ........................   Passed    0.10 sec  
        Start  11: core.program  
 11/140 Test  #11: core.program .........................***Failed    0.47 sec  
        Start  12: core.system  
 12/140 Test  #12: core.system ..........................   Passed    0.10 sec  
        Start  13: core.type_traits  
 13/140 Test  #13: core.type_traits .....................   Passed    0.00 sec  
        Start  14: core.user_event  
 14/140 Test  #14: core.user_event ......................***Failed    0.10 sec  
        Start  15: utility.extents  
 15/140 Test  #15: utility.extents ......................   Passed    0.10 sec  
        Start  16: utility.program_cache  
 16/140 Test  #16: utility.program_cache ................   Passed    0.20 sec  
        Start  17: utility.wait_list  
 17/140 Test  #17: utility.wait_list ....................   Passed    0.11 sec  
        Start  18: algorithm.accumulate  
 18/140 Test  #18: algorithm.accumulate .................   Passed    2.82 sec  
        Start  19: algorithm.adjacent_difference  
 19/140 Test  #19: algorithm.adjacent_difference ........***Failed    0.52 sec  
        Start  20: algorithm.adjacent_find  
 20/140 Test  #20: algorithm.adjacent_find ..............***Failed    0.92 sec  
        Start  21: algorithm.any_all_none_of  
 21/140 Test  #21: algorithm.any_all_none_of ............   Passed    1.90 sec  
        Start  22: algorithm.binary_search  
 22/140 Test  #22: algorithm.binary_search ..............   Passed    2.37 sec  
        Start  23: algorithm.copy  
 23/140 Test  #23: algorithm.copy .......................***Failed    0.10 sec  
        Start  24: algorithm.copy_if  
 24/140 Test  #24: algorithm.copy_if ....................***Failed    0.10 sec  
        Start  25: algorithm.count  
 25/140 Test  #25: algorithm.count ......................   Passed    4.06 sec  
        Start  26: algorithm.equal  
 26/140 Test  #26: algorithm.equal ......................***Failed    0.65 sec  
        Start  27: algorithm.equal_range  
 27/140 Test  #27: algorithm.equal_range ................   Passed    2.09 sec  
        Start  28: algorithm.extrema  
 28/140 Test  #28: algorithm.extrema ....................   Passed    1.71 sec  
        Start  29: algorithm.fill  
 29/140 Test  #29: algorithm.fill .......................***Failed    0.10 sec  
        Start  30: algorithm.find  
 30/140 Test  #30: algorithm.find .......................   Passed    1.44 sec  
        Start  31: algorithm.find_end  
 31/140 Test  #31: algorithm.find_end ...................   Passed    0.69 sec  
        Start  32: algorithm.for_each  
 32/140 Test  #32: algorithm.for_each ...................   Passed    0.52 sec  
        Start  33: algorithm.gather  
 33/140 Test  #33: algorithm.gather .....................   Passed    0.90 sec  
        Start  34: algorithm.generate  
 34/140 Test  #34: algorithm.generate ...................***Failed    0.10 sec  
        Start  35: algorithm.includes  
 35/140 Test  #35: algorithm.includes ...................   Passed    0.66 sec  
        Start  36: algorithm.inner_product  
 36/140 Test  #36: algorithm.inner_product ..............   Passed    0.69 sec  
        Start  37: algorithm.inplace_merge  
 37/140 Test  #37: algorithm.inplace_merge ..............   Passed    0.40 sec  
        Start  38: algorithm.inplace_reduce  
 38/140 Test  #38: algorithm.inplace_reduce .............   Passed    0.84 sec  
        Start  39: algorithm.insertion_sort  
 39/140 Test  #39: algorithm.insertion_sort .............   Passed    2.96 sec  
        Start  40: algorithm.iota  
 40/140 Test  #40: algorithm.iota .......................***Failed    0.79 sec  
        Start  41: algorithm.is_permutation  
 41/140 Test  #41: algorithm.is_permutation .............   Passed    0.79 sec  
        Start  42: algorithm.is_sorted  
 42/140 Test  #42: algorithm.is_sorted ..................***Failed    0.65 sec  
        Start  43: algorithm.merge  
 43/140 Test  #43: algorithm.merge ......................***Failed    0.10 sec  
        Start  44: algorithm.mismatch  
 44/140 Test  #44: algorithm.mismatch ...................***Failed    0.39 sec  
        Start  45: algorithm.next_permutation  
 45/140 Test  #45: algorithm.next_permutation ...........   Passed    1.56 sec  
        Start  46: algorithm.nth_element  
 46/140 Test  #46: algorithm.nth_element ................   Passed    6.25 sec  
        Start  47: algorithm.partial_sum  
 47/140 Test  #47: algorithm.partial_sum ................   Passed    0.38 sec  
        Start  48: algorithm.partition  
 48/140 Test  #48: algorithm.partition ..................   Passed    2.10 sec  
        Start  49: algorithm.partition_point  
 49/140 Test  #49: algorithm.partition_point ............   Passed    0.39 sec  
        Start  50: algorithm.prev_permutation  
 50/140 Test  #50: algorithm.prev_permutation ...........   Passed    1.70 sec  
        Start  51: algorithm.radix_sort  
 51/140 Test  #51: algorithm.radix_sort .................   Passed    4.08 sec  
        Start  52: algorithm.random_fill  
 52/140 Test  #52: algorithm.random_fill ................   Passed    1.24 sec  
        Start  53: algorithm.random_shuffle  
 53/140 Test  #53: algorithm.random_shuffle .............   Passed    0.52 sec  
        Start  54: algorithm.reduce  
 54/140 Test  #54: algorithm.reduce .....................***Failed    0.54 sec  
        Start  55: algorithm.reduce_by_key  
 55/140 Test  #55: algorithm.reduce_by_key ..............***Failed    0.40 sec  
        Start  56: algorithm.remove  
 56/140 Test  #56: algorithm.remove .....................   Passed    1.87 sec  
        Start  57: algorithm.replace  
 57/140 Test  #57: algorithm.replace ....................   Passed    0.53 sec  
        Start  58: algorithm.reverse  
 58/140 Test  #58: algorithm.reverse ....................   Passed    1.59 sec  
        Start  59: algorithm.rotate  
 59/140 Test  #59: algorithm.rotate .....................   Passed    0.10 sec  
        Start  60: algorithm.rotate_copy  
 60/140 Test  #60: algorithm.rotate_copy ................   Passed    0.10 sec  
        Start  61: algorithm.scan  
 61/140 Test  #61: algorithm.scan .......................***Failed    2.10 sec  
        Start  62: algorithm.scatter  
 62/140 Test  #62: algorithm.scatter ....................   Passed    0.52 sec  
        Start  63: algorithm.search  
 63/140 Test  #63: algorithm.search .....................   Passed    0.73 sec  
        Start  64: algorithm.search_n  
 64/140 Test  #64: algorithm.search_n ...................   Passed    0.76 sec  
        Start  65: algorithm.set_difference  
 65/140 Test  #65: algorithm.set_difference .............   Passed    0.99 sec  
        Start  66: algorithm.set_intersection  
 66/140 Test  #66: algorithm.set_intersection ...........   Passed    0.96 sec  
        Start  67: algorithm.set_symmetric_difference  
 67/140 Test  #67: algorithm.set_symmetric_difference ...   Passed    1.01 sec  
        Start  68: algorithm.set_union  
 68/140 Test  #68: algorithm.set_union ..................   Passed    1.02 sec  
        Start  69: algorithm.sort  
 69/140 Test  #69: algorithm.sort .......................   Passed    3.96 sec  
        Start  70: algorithm.sort_by_key  
 70/140 Test  #70: algorithm.sort_by_key ................   Passed    1.41 sec  
        Start  71: algorithm.stable_partition  
 71/140 Test  #71: algorithm.stable_partition ...........   Passed    1.15 sec  
        Start  72: algorithm.stable_sort  
 72/140 Test  #72: algorithm.stable_sort ................   Passed    1.25 sec  
        Start  73: algorithm.transform  
 73/140 Test  #73: algorithm.transform ..................   Passed    2.10 sec  
        Start  74: algorithm.transform_if  
 74/140 Test  #74: algorithm.transform_if ...............   Passed    0.80 sec  
        Start  75: algorithm.transform_reduce  
 75/140 Test  #75: algorithm.transform_reduce ...........   Passed    1.04 sec  
        Start  76: algorithm.unique  
 76/140 Test  #76: algorithm.unique .....................***Failed    0.44 sec  
        Start  77: algorithm.unique_copy  
 77/140 Test  #77: algorithm.unique_copy ................   Passed    0.39 sec  
        Start  78: algorithm.lexicographical_compare  
 78/140 Test  #78: algorithm.lexicographical_compare ....   Passed    0.76 sec  
        Start  79: allocator.buffer_allocator  
 79/140 Test  #79: allocator.buffer_allocator ...........   Passed    0.10 sec  
        Start  80: allocator.pinned_allocator  
 80/140 Test  #80: allocator.pinned_allocator ...........   Passed    0.10 sec  
        Start  81: async.wait  
 81/140 Test  #81: async.wait ...........................   Passed    0.10 sec  
        Start  82: async.wait_guard  
 82/140 Test  #82: async.wait_guard .....................***Failed    0.10 sec  
        Start  83: container.array  
 83/140 Test  #83: container.array ......................***Failed    0.10 sec  
        Start  84: container.dynamic_bitset  
 84/140 Test  #84: container.dynamic_bitset .............***Failed    0.10 sec  
        Start  85: container.flat_map  
 85/140 Test  #85: container.flat_map ...................   Passed    2.25 sec  
        Start  86: container.flat_set  
 86/140 Test  #86: container.flat_set ...................   Passed    1.62 sec  
        Start  87: container.mapped_view  
 87/140 Test  #87: container.mapped_view ................***Failed    0.10 sec  
        Start  88: container.stack  
 88/140 Test  #88: container.stack ......................   Passed    0.10 sec  
        Start  89: container.string  
 89/140 Test  #89: container.string .....................   Passed    0.83 sec  
        Start  90: container.valarray  
 90/140 Test  #90: container.valarray ...................***Failed    0.10 sec  
        Start  91: container.vector  
 91/140 Test  #91: container.vector .....................***Failed    0.40 sec  
        Start  92: exception.context_error  
 92/140 Test  #92: exception.context_error ..............   Passed    0.10 sec  
        Start  93: exception.no_device_found  
 93/140 Test  #93: exception.no_device_found ............   Passed    0.00 sec  
        Start  94: exception.opencl_error  
 94/140 Test  #94: exception.opencl_error ...............   Passed    0.10 sec  
        Start  95: exception.unsupported_extension  
 95/140 Test  #95: exception.unsupported_extension ......   Passed    0.00 sec  
        Start  96: functional.as  
 96/140 Test  #96: functional.as ........................***Failed    0.39 sec  
        Start  97: functional.bind  
 97/140 Test  #97: functional.bind ......................   Passed    1.72 sec  
        Start  98: functional.convert  
 98/140 Test  #98: functional.convert ...................   Passed    0.39 sec  
        Start  99: functional.get  
 99/140 Test  #99: functional.get .......................   Passed    0.00 sec  
        Start 100: functional.hash  
100/140 Test #100: functional.hash ......................   Passed    0.39 sec  
        Start 101: functional.identity  
101/140 Test #101: functional.identity ..................   Passed    0.39 sec  
        Start 102: functional.popcount  
102/140 Test #102: functional.popcount ..................   Passed    1.32 sec  
        Start 103: functional.unpack  
103/140 Test #103: functional.unpack ....................   Passed    0.66 sec  
        Start 104: image.image1d  
104/140 Test #104: image.image1d ........................***Failed    0.10 sec  
        Start 105: image.image2d  
105/140 Test #105: image.image2d ........................***Failed    0.11 sec  
        Start 106: image.image3d  
106/140 Test #106: image.image3d ........................   Passed    0.11 sec  
        Start 107: image.image_sampler  
107/140 Test #107: image.image_sampler ..................   Passed    0.10 sec  
        Start 108: iterator.buffer_iterator  
108/140 Test #108: iterator.buffer_iterator .............   Passed    0.39 sec  
        Start 109: iterator.constant_iterator  
109/140 Test #109: iterator.constant_iterator ...........   Passed    0.39 sec  
        Start 110: iterator.counting_iterator  
110/140 Test #110: iterator.counting_iterator ...........   Passed    0.39 sec  
        Start 111: iterator.discard_iterator  
111/140 Test #111: iterator.discard_iterator ............***Failed    0.10 sec  
        Start 112: iterator.function_input_iterator  
112/140 Test #112: iterator.function_input_iterator .....   Passed    0.39 sec  
        Start 113: iterator.permutation_iterator  
113/140 Test #113: iterator.permutation_iterator ........   Passed    0.39 sec  
        Start 114: iterator.strided_iterator  
114/140 Test #114: iterator.strided_iterator ............   Passed    0.66 sec  
        Start 115: iterator.transform_iterator  
115/140 Test #115: iterator.transform_iterator ..........   Passed    0.39 sec  
        Start 116: iterator.zip_iterator  
116/140 Test #116: iterator.zip_iterator ................   Passed    0.53 sec  
        Start 117: memory.local_buffer  
117/140 Test #117: memory.local_buffer ..................   Passed    0.10 sec  
        Start 118: memory.svm_ptr  
118/140 Test #118: memory.svm_ptr .......................***Failed    0.10 sec  
        Start 119: random.bernoulli_distribution  
119/140 Test #119: random.bernoulli_distribution ........   Passed    0.57 sec  
        Start 120: random.discrete_distribution  
120/140 Test #120: random.discrete_distribution .........   Passed    0.70 sec  
        Start 121: random.linear_congruential_engine  
121/140 Test #121: random.linear_congruential_engine ....   Passed    0.47 sec  
        Start 122: random.mersenne_twister_engine  
122/140 Test #122: random.mersenne_twister_engine .......   Passed    0.48 sec  
        Start 123: random.threefry_engine  
123/140 Test #123: random.threefry_engine ...............***Failed    0.49 sec  
        Start 124: random.normal_distribution  
124/140 Test #124: random.normal_distribution ...........   Passed    0.58 sec  
        Start 125: random.uniform_int_distribution  
125/140 Test #125: random.uniform_int_distribution ......   Passed    1.91 sec  
        Start 126: random.uniform_real_distribution  
126/140 Test #126: random.uniform_real_distribution .....   Passed    0.70 sec  
        Start 127: types.fundamental  
127/140 Test #127: types.fundamental ....................   Passed    0.00 sec  
        Start 128: types.complex  
128/140 Test #128: types.complex ........................***Failed    0.10 sec  
        Start 129: types.pair  
129/140 Test #129: types.pair ...........................***Failed    0.39 sec  
        Start 130: types.tuple  
130/140 Test #130: types.tuple ..........................   Passed    0.78 sec  
        Start 131: types.struct  
131/140 Test #131: types.struct .........................   Passed    0.65 sec  
        Start 132: type_traits.result_of  
132/140 Test #132: type_traits.result_of ................   Passed    0.00 sec  
        Start 133: experimental.clamp_range  
133/140 Test #133: experimental.clamp_range .............   Passed    0.39 sec  
        Start 134: experimental.malloc  
134/140 Test #134: experimental.malloc ..................   Passed    0.10 sec  
        Start 135: experimental.sort_by_transform  
135/140 Test #135: experimental.sort_by_transform .......   Passed    0.93 sec  
        Start 136: experimental.tabulate  
136/140 Test #136: experimental.tabulate ................   Passed    0.39 sec  
        Start 137: misc.amd_cpp_kernel_language  
137/140 Test #137: misc.amd_cpp_kernel_language .........   Passed    0.10 sec  
        Start 138: misc.lambda  
138/140 Test #138: misc.lambda ..........................   Passed    2.83 sec  
        Start 139: misc.user_defined_types  
139/140 Test #139: misc.user_defined_types ..............   Passed    0.39 sec  
        Start 140: misc.multiple_objects  
140/140 Test #140: misc.multiple_objects ................   Passed    0.00 sec  
  
75% tests passed, 35 tests failed out of 140  
  
Total Test time (real) = 108.19 sec  
  
The following tests FAILED:  
      1 - core.buffer (Failed)  
      2 - core.closure (Failed)  
      3 - core.command_queue (Failed)  
      9 - core.pipe (Failed)  
     11 - core.program (Failed)  
     14 - core.user_event (Failed)  
     19 - algorithm.adjacent_difference (Failed)  
     20 - algorithm.adjacent_find (Failed)  
     23 - algorithm.copy (Failed)  
     24 - algorithm.copy_if (Failed)  
     26 - algorithm.equal (Failed)  
     29 - algorithm.fill (Failed)  
     34 - algorithm.generate (Failed)  
     40 - algorithm.iota (Failed)  
     42 - algorithm.is_sorted (Failed)  
     43 - algorithm.merge (Failed)  
     44 - algorithm.mismatch (Failed)  
     54 - algorithm.reduce (Failed)  
     55 - algorithm.reduce_by_key (Failed)  
     61 - algorithm.scan (Failed)  
     76 - algorithm.unique (Failed)  
     82 - async.wait_guard (Failed)  
     83 - container.array (Failed)  
     84 - container.dynamic_bitset (Failed)  
     87 - container.mapped_view (Failed)  
     90 - container.valarray (Failed)  
     91 - container.vector (Failed)  
     96 - functional.as (Failed)  
    104 - image.image1d (Failed)  
    105 - image.image2d (Failed)  
    111 - iterator.discard_iterator (Failed)  
    118 - memory.svm_ptr (Failed)  
    123 - random.threefry_engine (Failed)  
    128 - types.complex (Failed)  
    129 - types.pair (Failed)  
Makefile:96: recipe for target 'test' failed  
  
```

---

## Comment 3

> Username: ghisvail  
> Created at: 2015-08-10 10:51:44 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-129405585  

my `clinfo`:  
  
```  
Number of platforms                               1  
  Platform Name                                   Portable Computing Language  
  Platform Vendor                                 The pocl project  
  Platform Version                                OpenCL 1.2 pocl 0.10  
  Platform Profile                                FULL_PROFILE  
  Platform Extensions                             cl_khr_icd  
  Platform Extensions function suffix             POCL  
  
  Platform Name                                   Portable Computing Language  
Number of devices                                 1  
  Device Name                                     pthread-Intel(R) Core(TM) i7-4710MQ CPU @ 2.50GHz  
  Device Vendor                                   pocl  
  Device Vendor ID                                0x0  
  Device Version                                  OpenCL 1.2 pocl  
  Driver Version                                  0.10  
  Device OpenCL C Version                         OpenCL C 1.2  
  Device Type                                     CPU, Default  
  Device Profile                                  FULL_PROFILE  
  Max compute units                               8  
  Max clock frequency                             3500MHz  
  Device Partition                                (core)  
    Max number of sub-devices                     1  
    Supported partition types                     None  
  Max work item dimensions                        3  
  Max work item sizes                             1024x1024x1024  
  Max work group size                             1024  
  Preferred work group size multiple              8  
  Preferred / native vector sizes                   
    char                                                16 / 16        
    short                                                8 / 8         
    int                                                  4 / 4         
    long                                                 2 / 2         
    half                                                 8 / 8        (n/a)  
    float                                                4 / 4         
    double                                               2 / 2        (cl_khr_fp64)  
  Half-precision Floating-point support           (n/a)  
  Single-precision Floating-point support         (core)  
    Denormals                                     No  
    Infinity and NANs                             Yes  
    Round to nearest                              Yes  
    Round to zero                                 No  
    Round to infinity                             No  
    IEEE754-2008 fused multiply-add               No  
    Support is emulated in software               No  
    Correctly-rounded divide and sqrt operations  No  
  Double-precision Floating-point support         (cl_khr_fp64)  
    Denormals                                     No  
    Infinity and NANs                             Yes  
    Round to nearest                              Yes  
    Round to zero                                 No  
    Round to infinity                             No  
    IEEE754-2008 fused multiply-add               No  
    Support is emulated in software               No  
    Correctly-rounded divide and sqrt operations  No  
  Address bits                                    64, Little-Endian  
  Global memory size                              8052850688 (7.5GiB)  
  Error Correction support                        No  
  Max memory allocation                           2013212672 (1.875GiB)  
  Unified memory for Host and Device              Yes  
  Minimum alignment for any data type             128 bytes  
  Alignment of base address                       128 bits (16 bytes)  
  Global Memory cache type                        None  
  Image support                                   Yes  
    Max number of samplers per kernel             16  
    Max size for 1D images from buffer            0 pixels  
    Max 1D or 2D image array size                 0 images  
    Max 2D image size                             8192x8192 pixels  
    Max 3D image size                             2048x2048x2048 pixels  
    Max number of read image args                 128  
    Max number of write image args                128  
  Local memory type                               Global  
  Local memory size                               2013212672 (1.875GiB)  
  Max constant buffer size                        2013212672 (1.875GiB)  
  Max number of constant args                     8  
  Max size of kernel argument                     1024  
  Queue properties                                  
    Out-of-order execution                        No  
    Profiling                                     Yes  
  Prefer user sync for interop                    Yes  
  Profiling timer resolution                      0ns  
  Execution capabilities                            
    Run OpenCL kernels                            Yes  
    Run native kernels                            Yes  
  printf() buffer size                            0  
  Built-in kernels                                  
  Device Available                                Yes  
  Compiler Available                              Yes  
  Linker Available                                Yes  
  Device Extensions                               cl_khr_fp64 cl_khr_byte_addressable_store  
  
NULL platform behavior  
  clGetPlatformInfo(NULL, CL_PLATFORM_NAME, ...)  Portable Computing Language  
  clGetDeviceIDs(NULL, CL_DEVICE_TYPE_ALL, ...)   Success [POCL]  
  clCreateContext(NULL, ...) [default]            Success [POCL]  
  clCreateContextFromType(NULL, CL_DEVICE_TYPE_CPU)  Success (1)  
    Platform Name                                 Portable Computing Language  
    Device Name                                   pthread-Intel(R) Core(TM) i7-4710MQ CPU @ 2.50GHz  
  clCreateContextFromType(NULL, CL_DEVICE_TYPE_GPU)  No devices found in platform  
  clCreateContextFromType(NULL, CL_DEVICE_TYPE_ACCELERATOR)  No devices found in platform  
  clCreateContextFromType(NULL, CL_DEVICE_TYPE_CUSTOM)  No devices found in platform  
  clCreateContextFromType(NULL, CL_DEVICE_TYPE_ALL)  Success (1)  
    Platform Name                                 Portable Computing Language  
    Device Name                                   pthread-Intel(R) Core(TM) i7-4710MQ CPU @ 2.50GHz  
  
ICD loader properties  
  ICD loader Name                                 OpenCL ICD Loader  
  ICD loader Vendor                               OCL Icd free software  
  ICD loader Version                              2.2.7  
  ICD loader Profile                              OpenCL 1.2  
  
```

---

## Comment 4

> Username: pavanky  
> Created at: 2015-08-10 11:33:48 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-129413843  

@ghisvail This could be a bug in either Boost Compute or pocl. I have noticed arrayfire tests that pass on almost all proprietary OpenCL implementations fail on pocl, Gallium Compute, and Beignet. It could potentially be the maturity of the OpenCL implementations rather than the libraries.  
  
Can you try cross posting the same issue at pocl as well ?

---

## Comment 5

> Username: ghisvail  
> Created at: 2015-08-10 11:44:37 UTC  
> Updated at: 2015-08-10 11:44:48 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-129418628  

> Can you try cross posting the same issue at pocl as well ?  
  
@pavanky I will  
  
To be honest, the test suite does not pass completely on my Nvidia drivers either.

---

## Comment 6

> Username: pavanky  
> Created at: 2015-08-10 11:45:16 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-129418999  

Having said that I think it would be great to have stable Open Source OpenCL implementations and also make sure the libraries Boost Compute and ArrayFire are not missing something on our parts. Perhaps we should try and talk to all parties involved to have a centralized place to track issues like this.  
  
@kylelutz @pjaaskel Do you guys have any suggestions about this?

---

## Comment 7

> Username: ghisvail  
> Created at: 2015-08-10 11:50:04 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-129419931  

FYI, the current version of pocl packaged in Debian is not the latest at the moment (v0.10, whilst v0.11 is available upstream).

---

## Comment 8

> Username: pjaaskel  
> Created at: 2015-08-10 11:56:25 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-129421102  

If you suspect a pocl bug, please first try the latest master revision of pocl with the latest supported Clang/LLVM.

---

## Comment 9

> Username: jszuppe  
> Created at: 2015-08-10 21:23:51 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-129611410  

I'll try to look into it later this week. Maybe I'll find where is the problem.

---

## Comment 10

> Username: kylelutz  
> Created at: 2015-08-11 04:54:36 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-129704487  

@ghisvail Interesting to see some of the core tests fail (like `core.buffer`). Could you post the output of `ctest -R core.* -V` on `develop`?

---

## Comment 11

> Username: ghisvail  
> Created at: 2015-08-11 09:05:37 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-129781936  

> Could you post the output of ctest -R core.\* -V on develop?  
  
```  
UpdateCTestConfiguration  from :/home/ghislain/workspace/compute/build/DartConfiguration.tcl  
UpdateCTestConfiguration  from :/home/ghislain/workspace/compute/build/DartConfiguration.tcl  
Test project /home/ghislain/workspace/compute/build  
Constructing a list of tests  
Done constructing a list of tests  
Checking test dependency graph...  
Checking test dependency graph end  
test 1  
      Start  1: core.buffer  
  
1: Test command: /home/ghislain/workspace/compute/build/test/test_buffer  
1: Test timeout computed to be: 9.99988e+06  
1: pocl error: encountered unimplemented part of the OpenCL specs in clSetMemObjectDestructorCallback.c:8  
1: Running 10 test cases...  
 1/14 Test  #1: core.buffer ......................***Failed    0.02 sec  
test 2  
      Start  2: core.closure  
  
2: Test command: /home/ghislain/workspace/compute/build/test/test_closure  
2: Test timeout computed to be: 9.99988e+06  
2: Running 7 test cases...  
2: unknown location(0): fatal error in "scale_add_vec": memory access violation at address: 0x00000000: no mapping at fault address  
2: /home/ghislain/workspace/compute/test/test_closure.cpp(67): last checkpoint  
2:   
2: *** 1 failure detected in test suite "TestClosure"  
 2/14 Test  #2: core.closure .....................***Failed    0.92 sec  
test 3  
      Start  3: core.command_queue  
  
3: Test command: /home/ghislain/workspace/compute/build/test/test_command_queue  
3: Test timeout computed to be: 9.99988e+06  
3: Running 10 test cases...  
3: unknown location(0): fatal error in "construct_from_cl_command_queue": memory access violation at address: 0x00000000: no mapping at fault address  
3: /home/ghislain/workspace/compute/test/test_command_queue.cpp(134): last checkpoint  
3:   
3: *** 1 failure detected in test suite "TestCommandQueue"  
 3/14 Test  #3: core.command_queue ...............***Failed    0.31 sec  
test 4  
      Start  4: core.context  
  
4: Test command: /home/ghislain/workspace/compute/build/test/test_context  
4: Test timeout computed to be: 9.99988e+06  
4: Running 2 test cases...  
4:   
4: *** No errors detected  
 4/14 Test  #4: core.context .....................   Passed    0.01 sec  
test 5  
      Start  5: core.device  
  
5: Test command: /home/ghislain/workspace/compute/build/test/test_device  
5: Test timeout computed to be: 9.99988e+06  
5: Running 11 test cases...  
5: skipping test: device does not support CL_DEVICE_PARTITION_EQUALLY  
5: skipping test: device does not support CL_DEVICE_PARTITION_BY_COUNTS  
5: skipping test: device does not support partitioning by affinity domain  
5: pthread-Intel(R) Core(TM) i5-4300M CPU @ 2.60GHz  
5:   
5: *** No errors detected  
 5/14 Test  #5: core.device ......................   Passed    0.01 sec  
test 6  
      Start  6: core.event  
  
6: Test command: /home/ghislain/workspace/compute/build/test/test_event  
6: Test timeout computed to be: 9.99988e+06  
6: Running 1 test case...  
6:   
6: *** No errors detected  
 6/14 Test  #6: core.event .......................   Passed    0.01 sec  
test 7  
      Start  7: core.function  
  
7: Test command: /home/ghislain/workspace/compute/build/test/test_function  
7: Test timeout computed to be: 9.99988e+06  
7: Running 7 test cases...  
7:   
7: *** No errors detected  
 7/14 Test  #7: core.function ....................   Passed    1.63 sec  
test 8  
      Start  8: core.kernel  
  
8: Test command: /home/ghislain/workspace/compute/build/test/test_kernel  
8: Test timeout computed to be: 9.99988e+06  
8: Running 5 test cases...  
8:   
8: *** No errors detected  
 8/14 Test  #8: core.kernel ......................   Passed    0.79 sec  
test 9  
      Start  9: core.pipe  
  
9: Test command: /home/ghislain/workspace/compute/build/test/test_pipe  
9: Test timeout computed to be: 9.99988e+06  
9: Running 2 test cases...  
9: unknown location(0): fatal error in "create_pipe": memory access violation at address: 0x00000000: no mapping at fault address  
9:   
9: *** 1 failure detected in test suite "TestPipe"  
 9/14 Test  #9: core.pipe ........................***Failed    0.01 sec  
test 10  
      Start 10: core.platform  
  
10: Test command: /home/ghislain/workspace/compute/build/test/test_platform  
10: Test timeout computed to be: 9.99988e+06  
10: Running 2 test cases...  
10:   
10: *** No errors detected  
10/14 Test #10: core.platform ....................   Passed    0.01 sec  
test 11  
      Start 11: core.program  
  
11: Test command: /home/ghislain/workspace/compute/build/test/test_program  
11: Test timeout computed to be: 9.99988e+06  
11: Running 7 test cases...  
11: unknown location(0): fatal error in "compile_and_link": memory access violation at address: 0x00000000: no mapping at fault address  
11: /home/ghislain/workspace/compute/test/test_program.cpp(100): last checkpoint  
11:   
11: *** 1 failure detected in test suite "TestProgram"  
11/14 Test #11: core.program .....................***Failed    0.41 sec  
test 12  
      Start 12: core.system  
  
12: Test command: /home/ghislain/workspace/compute/build/test/test_system  
12: Test timeout computed to be: 9.99988e+06  
12: Running 4 test cases...  
12:   
12: *** No errors detected  
12/14 Test #12: core.system ......................   Passed    0.01 sec  
test 13  
      Start 13: core.type_traits  
  
13: Test command: /home/ghislain/workspace/compute/build/test/test_type_traits  
13: Test timeout computed to be: 9.99988e+06  
13: Running 9 test cases...  
13:   
13: *** No errors detected  
13/14 Test #13: core.type_traits .................   Passed    0.00 sec  
test 14  
      Start 14: core.user_event  
  
14: Test command: /home/ghislain/workspace/compute/build/test/test_user_event  
14: Test timeout computed to be: 9.99988e+06  
14: pocl error: encountered unimplemented part of the OpenCL specs in clCreateUserEvent.c:8  
14: Running 2 test cases...  
14/14 Test #14: core.user_event ..................***Failed    0.01 sec  
  
The following tests passed:  
    core.context  
    core.device  
    core.event  
    core.function  
    core.kernel  
    core.platform  
    core.system  
    core.type_traits  
  
57% tests passed, 6 tests failed out of 14  
  
Total Test time (real) =   4.16 sec  
  
The following tests FAILED:  
      1 - core.buffer (Failed)  
      2 - core.closure (Failed)  
      3 - core.command_queue (Failed)  
      9 - core.pipe (Failed)  
     11 - core.program (Failed)  
     14 - core.user_event (Failed)  
  
```

---

## Comment 12

> Username: pjaaskel  
> Created at: 2015-08-12 20:10:38 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-130430054  

clCreateUserEvent.c:8 doesn't have ABORT_UNIMPLEMENTED in pocl master so pls retry with the latest pocl revision.

---

## Comment 13

> Username: ghisvail  
> Created at: 2015-08-12 22:16:43 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-130463261  

> pls retry with the latest pocl revision.  
  
That is v0.11 ?

---

## Comment 14

> Username: pjaaskel  
> Created at: 2015-08-13 06:37:00 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-130555638  

This one seems to be fixed in 0.11, but we are already closing 0.12 release which should be branched soon.

---

## Comment 15

> Username: ghisvail  
> Created at: 2015-08-13 07:34:49 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-130565450  

Good, then I'll poke the Debian maintainer of `pocl` (perhaps offering some help) to get the latest version out as soon as possible.

---

## Comment 16

> Username: jszuppe  
> Created at: 2016-02-13 19:47:12 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-183737453  

I'm trying to make POCL build on Travis CI, but I've run into a problem. I keep getting `undefined reference to `cl<function_name>'` (for example `undefined reference to `clEnqueueSVMMemcpy'`) with both OpenCL 2.0 and 1.2 headers (I did not testes 1.1 OpenCL headers). It looks like POCL `libOpenCL.so` is built without OpenCL 2.0 and 1.2 functions, but I don't know why. I build POCL from master branch. I use direct linkage, no ICD. I have the same errors on my PC too.  
  
See https://travis-ci.org/haahh/compute/builds/109049372#L2641 and my travis script https://github.com/haahh/compute/blob/update-travis-ci-pocl2/.travis.yml.

---

## Comment 17

> Username: pjaaskel  
> Created at: 2016-02-22 09:31:02 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-187092275  

@haahh please open a separate issue. But I think you might have 1.1 headers installed in path somewhere and it builds against those. You can try --enable-install-opencl-headers

---

## Comment 18

> Username: jszuppe  
> Created at: 2016-02-22 09:46:52 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-187097825  

@pjaaskel Thanks for the suggestion. I thought if cmake prints `OpenCL.h not found, installing our headers` it uses headers included in POCL (OpenCL 2.0), but nonetheless I will check that. If headers are >1.1, I'll open an issue.

---

## Comment 19

> Username: jszuppe  
> Created at: 2016-03-02 22:21:54 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-191459870  

Boost.Compute works with POCL with OpenCL 1.0 and 1.1 headers (see Travis-CI builds). Builds with OpenCL 1.2 and 2.0 headers are not set up because POCL does not implement all OpenCL 1.2 and 2.0 functions (causing link problems) and I simply did not have time to investigate Travis-CI build with POCL via AMD/Khronos ICD.

---

## Comment 20

> Username: jszuppe  
> Created at: 2016-10-12 21:03:27 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-253338470  

This can be closed. Boost.Compute works with POCL (with OpenCL 1.0 and 1.1).

---

## Comment 21

> Username: kylelutz  
> Created at: 2016-10-14 02:00:44 UTC  
> Url: https://github.com/boostorg/compute/issues/493#issuecomment-253690734  

Closing. @ghisvail please re-open if you're still encountering issues with the latest release.
