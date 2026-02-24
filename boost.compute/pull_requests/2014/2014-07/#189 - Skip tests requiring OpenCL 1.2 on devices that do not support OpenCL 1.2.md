# #189 Skip tests requiring OpenCL 1.2 on devices that do not support OpenCL 1.2 [Merged]

> Username: ddemidov  
> Created at: 2014-07-12 18:50:41 UTC  
> Updated at: 2014-07-12 19:50:51 UTC  
> Merged at: 2014-07-12 19:25:54 UTC  
> Closed at: 2014-07-12 19:25:54 UTC  
> Url: https://github.com/boostorg/compute/pull/189  

This introduces macro `REQUIRES_OPENCL_VERSION(major,minor)` and marks the tests that require OpenCL 1.2 with it.  
  
This is based on #188. I'll rebase on develop when that gets merged, or you can directly merge this and skip #188 altogether.  
  
This and #188 combined fix majority of failing tests on Tesla K40, except for   
  
```  
     27 - algorithm.extrema (Failed)  
     71 - algorithm.transform (Failed)  
```  
  
These failures look different:  
  
```  
$ ./test_extrema   
Running 4 test cases...  
Boost.Compute: kernel compilation failed (-42)  
--- source ---  
#define boost_pair_type(t1, t2) _pair_ ## t1 ## _ ## t2 ## _t  
#define boost_pair_get(x, n) (n == 0 ? x.first ## x.second)  
#define boost_make_pair(t1, x, t2, y) (boost_pair_type(t1, t2)) { x, y }  
#define boost_tuple_get(x, n) (x.v ## n)  
  
  
__kernel void serial_find_extrema(__global uint* _buf0, __global uint* index, uint size)  
{  
uint value = popcount(_buf0[0]);  
uint value_index = 0;  
for(uint i = 1; i < size; i++){  
  uint candidate=popcount(_buf0[i]);  
  if(candidate>value){  
    value = candidate;  
    value_index = i;  
  }  
}  
*index = value_index;  
  
}  
  
--- build log ---  
ptxas application ptx input, line 36; error   : Call has wrong number of parameters  
ptxas application ptx input, line 65; error   : Call has wrong number of parameters  
ptxas fatal   : Ptx assembly aborted due to errors  
  
/home/demidov/work/opencl/compute/include/boost/compute/program.hpp(266): fatal error in "void boost::compute::program::build(const string&)": std::exception: Invalid Binary  
/home/demidov/work/opencl/compute/test/test_extrema.cpp(146): last checkpoint  
  
*** 1 failure detected in test suite "TestExtrema"  
$ ./test_transform  
Running 11 test cases...  
Boost.Compute: kernel compilation failed (-42)  
--- source ---  
#define boost_pair_type(t1, t2) _pair_ ## t1 ## _ ## t2 ## _t  
#define boost_pair_get(x, n) (n == 0 ? x.first ## x.second)  
#define boost_make_pair(t1, x, t2, y) (boost_pair_type(t1, t2)) { x, y }  
#define boost_tuple_get(x, n) (x.v ## n)  
  
  
__kernel void copy(uint count, __global uint* _buf0, __global uint* _buf1)  
{  
uint index = get_local_id(0) + (512 * get_group_id(0));  
for(uint i = 0; i < 4; i++){  
    if(index < count){  
_buf0[index]=popcount(_buf1[index]);  
        index += 128;  
    }  
}  
  
}  
  
--- build log ---  
ptxas application ptx input, line 58; error   : Call has wrong number of parameters  
ptxas fatal   : Ptx assembly aborted due to errors  
  
/home/demidov/work/opencl/compute/include/boost/compute/program.hpp(266): fatal error in "void boost::compute::program::build(const string&)": std::exception: Invalid Binary  
/home/demidov/work/opencl/compute/test/test_transform.cpp(204): last checkpoint  
  
*** 1 failure detected in test suite "TestTransform"  
```

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-07-12 18:56:20 UTC  
> Url: https://github.com/boostorg/compute/pull/189#issuecomment-48820808  

Cool! Thanks! And yeah, the `popcount()` issue is different. This is caused by us injecting a call to the built-in `popcount()` if `CL_VERSION_1_2` is defined. I'll add a check for the OpenCL version and insert the generic popcount() implementation if the device doesn't support OpenCL 1.2.

---

## Comment 2

> Username: coveralls  
> Created_at: 2014-07-12 19:25:48 UTC  
> Url: https://github.com/boostorg/compute/pull/189#issuecomment-48821552  

[![Coverage Status](https://coveralls.io/builds/960565/badge)](https://coveralls.io/builds/960565)  
  
Coverage decreased (-0.01%) when pulling **201f85a0382e31ab0367b2b49b02caddadcb9757 on ddemidov:skip-1.2-tests** into **2540895ebf6cb488b01604097afdf68f35aaf5d4 on kylelutz:develop**.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-07-12 19:50:51 UTC  
> Url: https://github.com/boostorg/compute/pull/189#issuecomment-48822183  

PR #191 should fix the issues with `popcount()`.

---
