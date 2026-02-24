# #550 - Kernel compilation fails for compute::count [Closed]

> Username: 0x47  
> Created at: 2016-01-12 14:38:52 UTC  
> Updated at: 2016-01-12 21:52:20 UTC  
> Closed at: 2016-01-12 21:45:38 UTC  
> Url: https://github.com/boostorg/compute/issues/550  

I use this simple code with `OpenCL 1.2 CUDA` on a GeForce GTX 750 Ti:  
  
```  
BOOST_COMPUTE_FUNCTION(bool, tuple_is_equal, (boost::tuple<uchar, uchar> x),  
{  
   return boost_tuple_get(x, 0) == boost_tuple_get(x, 1);  
});  
  
// ...  
  
std::vector<compute::vector<uchar>> d_blender_images;  
compute::vector<uchar> d_frame(ctx);  
  
// ...  
  
compute::vector<bool> d_frame_2(d_frame.size(), ctx);  
  
// ...  
  
compute::transform(  
   compute::make_zip_iterator(boost::make_tuple(d_frame.begin(), d_blender_images.at(j).begin())),  
   compute::make_zip_iterator(boost::make_tuple(d_frame.end(), d_blender_images.at(j).end())),  
   d_frame_2.begin(),  
   tuple_is_equal,  
   queue  
);  
int count = compute::count(d_frame_2.begin(), d_frame_2.end(), true, queue);  
```  
  
Which throws an exception after building the second kernel:  
  
```  
Boost.Compute: kernel compilation failed (-5)  
--- source ---  
#define boost_pair_type(t1, t2) _pair_ ## t1 ## _ ## t2 ## _t  
#define boost_pair_get(x, n) (n == 0 ? x.first ## x.second)  
#define boost_make_pair(t1, x, t2, y) (boost_pair_type(t1, t2)) { x, y }  
#define boost_tuple_get(x, n) (x.v ## n)  
  
  
__kernel void reduce(__global ulong* input, const uint offset, const uint count, __global ulong* output, const uint output_offset)  
{  
const uint block_offset = get_group_id(0) * VPT * TPB;  
__global const ulong *block = input + offset + block_offset;  
const uint lid = get_local_id(0);  
__local ulong scratch[TPB];  
ulong sum = 0;  
for(uint i = 0; i < VPT; i++){  
    if(block_offset + lid + i*TPB < count){  
        sum = sum + block[lid+i*TPB];  
    }  
}  
scratch[lid] = sum;  
barrier(CLK_LOCAL_MEM_FENCE);  
if(TPB >= 1024){  
if(lid < 512) { sum += scratch[lid + 512]; scratch[lid] = sum;} barrier(CLK_LOCAL_MEM_FENCE);}  
if(TPB >= 512){  
if(lid < 256) { sum += scratch[lid + 256]; scratch[lid] = sum;} barrier(CLK_LOCAL_MEM_FENCE);}  
if(TPB >= 256){  
if(lid < 128) { sum += scratch[lid + 128]; scratch[lid] = sum;} barrier(CLK_LOCAL_MEM_FENCE);}  
if(TPB >= 128){  
if(lid < 64) { sum += scratch[lid + 64]; scratch[lid] = sum;} barrier(CLK_LOCAL_MEM_FENCE);}  
if(lid < 32){  
volatile __local ulong *lmem = scratch;  
if(TPB >= 64) { lmem[lid] = sum = sum + lmem[lid+32];}  
if(TPB >= 32) { lmem[lid] = sum = sum + lmem[lid+16];}  
if(TPB >= 16) { lmem[lid] = sum = sum + lmem[lid+ 8];}  
if(TPB >=  8) { lmem[lid] = sum = sum + lmem[lid+ 4];}  
if(TPB >=  4) { lmem[lid] = sum = sum + lmem[lid+ 2];}  
if(TPB >=  2) { lmem[lid] = sum = sum + lmem[lid+ 1];}  
}  
if(lid == 0){  
    output[output_offset + get_group_id(0)] = scratch[0];  
}  
  
}  
  
--- build log ---  
  
ptxas info    : 0 bytes gmem  
ptxas info    : Compiling entry function 'reduce' for 'sm_50'  
ptxas info    : Function properties for reduce  
ptxas         .     0 bytes stack frame, 0 bytes spill stores, 0 bytes spill loads  
ptxas info    : Used 32 registers, 1032 bytes smem, 348 bytes cmem[0]  
```  
  
I'm not 100% sure, but I think the code should be correct judging from the documentation.  
The compiler call is:  
  
```  
cl_int ret = clBuildProgram(m_program, 0, 0, "-DT=ulong -DVPT=8 -DTPB=128 -cl-nv-verbose", 0, 0);  
```  
  
I added `-cl-nv-verbose` to enable the ptxas info (build log) output.

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-01-12 16:26:27 UTC  
> Updated at: 2016-01-12 16:39:13 UTC  
> Url: https://github.com/boostorg/compute/issues/550#issuecomment-170964698  

The error code is `-5` which means that the error is `CL_OUT_OF_RESOURCES` (there is a failure to allocate resources required by the OpenCL implementation on the device). Maybe there is no enough local memory or registers.  
  
`compute::vector<bool> d_frame_2(d_frame.size(), ctx);`  
  
Basically in OpenCL you shouldn't have bool buffers, because the size of bool is implementation defined and and in addition can also be different for the OpenCL device and the host. Use `boost::compute::uint_` type or `boost::compute::uchar`. I can see that in this implementation it's ulong type which is not memory-efficient.  
  
##   
  
I'm also not sure if it is good that we use `ulong_` type in [count_if_with_reduce.hpp](https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/detail/count_if_with_reduce.hpp). Because of that local memory type is ulong_ and that costs time and space. I think it would be wiser to check the size of vector: if it's less than UINT_MAX use `uint_`, if it's more I might be better to just call `boost::compute::reduce()` several times and add the results.

---

## Comment 2

> Username: 0x47  
> Created at: 2016-01-12 17:19:50 UTC  
> Url: https://github.com/boostorg/compute/issues/550#issuecomment-170980710  

I think local memory or registers cannot be the issue, at least not if the build log is correct. 32 registers and 1kB s(hared?)mem...  
  
I changed   
  
```  
compute::vector<bool> d_frame_2(d_frame.size(), ctx);  
```  
  
to  
  
```  
compute::vector<compute::uchar_> d_frame_2(d_frame.size(), ctx);  
```  
  
and updated `tuple_is_equal` accordingly. Unfortunately the generated kernel code remains the same (with ulong):  
  
```  
#define boost_pair_type(t1, t2) _pair_ ## t1 ## _ ## t2 ## _t  
#define boost_pair_get(x, n) (n == 0 ? x.first ## x.second)  
#define boost_make_pair(t1, x, t2, y) (boost_pair_type(t1, t2)) { x, y }  
#define boost_tuple_get(x, n) (x.v ## n)  
  
  
__kernel void reduce(__global ulong* input, const uint offset, const uint count, __global ulong* output, const uint output_offset)  
{  
const uint block_offset = get_group_id(0) * VPT * TPB;  
__global const ulong *block = input + offset + block_offset;  
const uint lid = get_local_id(0);  
__local ulong scratch[TPB];  
ulong sum = 0;  
for(uint i = 0; i < VPT; i++){  
    if(block_offset + lid + i*TPB < count){  
        sum = sum + block[lid+i*TPB];   
    }  
}  
scratch[lid] = sum;  
barrier(CLK_LOCAL_MEM_FENCE);  
if(TPB >= 1024){  
if(lid < 512) { sum += scratch[lid + 512]; scratch[lid] = sum;} barrier(CLK_LOCAL_MEM_FENCE);}  
if(TPB >= 512){  
if(lid < 256) { sum += scratch[lid + 256]; scratch[lid] = sum;} barrier(CLK_LOCAL_MEM_FENCE);}  
if(TPB >= 256){  
if(lid < 128) { sum += scratch[lid + 128]; scratch[lid] = sum;} barrier(CLK_LOCAL_MEM_FENCE);}  
if(TPB >= 128){  
if(lid < 64) { sum += scratch[lid + 64]; scratch[lid] = sum;} barrier(CLK_LOCAL_MEM_FENCE);}   
if(lid < 32){  
volatile __local ulong *lmem = scratch;  
if(TPB >= 64) { lmem[lid] = sum = sum + lmem[lid+32];}   
if(TPB >= 32) { lmem[lid] = sum = sum + lmem[lid+16];}   
if(TPB >= 16) { lmem[lid] = sum = sum + lmem[lid+ 8];}   
if(TPB >=  8) { lmem[lid] = sum = sum + lmem[lid+ 4];}   
if(TPB >=  4) { lmem[lid] = sum = sum + lmem[lid+ 2];}   
if(TPB >=  2) { lmem[lid] = sum = sum + lmem[lid+ 1];}   
}  
if(lid == 0){  
    output[output_offset + get_group_id(0)] = scratch[0];  
}  
  
}  
```  
  
Here is the complete source code, in case some important part is missing: http://dpaste.com/0EM61SK

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-01-12 17:53:08 UTC  
> Updated at: 2016-01-12 17:53:42 UTC  
> Url: https://github.com/boostorg/compute/issues/550#issuecomment-170992349  

Yes, you're right. In that kernel ulong_ type is used, becouse `ulong_` type is used in [count_if_with_reduce.hpp](https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/detail/count_if_with_reduce.hpp). But before `reduce` kernel there is also `initial_reduce` kernel (see [reduce_on_gpu.hpp](https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/detail/reduce_on_gpu.hpp), but that's not the problem here. Nonetheless, it's better not to use `bool` type in device vectors.  
  
I'll try to compile that code later today and see what happens.

---

## Comment 4

> Username: 0x47  
> Created at: 2016-01-12 21:39:24 UTC  
> Updated at: 2016-01-12 21:45:35 UTC  
> Url: https://github.com/boostorg/compute/issues/550#issuecomment-171066617  

Funny, the code works perfectly fine on my Laptop (OpenCL 1.1 CUDA on a GeForce GT 540M). It had a few bugs, here is the new/final version: http://dpaste.com/1V1J7FA  
  
Note that I switched from `transform`+`count` to `count_if` (see commented code), but both variants have the same error on my desktop computer.  
  
EDIT: It works on my Desktop, too. That's embarrassing, sorry. I must have accidentally fixed some bug I had while working at on my laptop. Thanks for your help!

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-01-12 21:49:27 UTC  
> Updated at: 2016-01-12 21:52:20 UTC  
> Url: https://github.com/boostorg/compute/issues/550#issuecomment-171069079  

``` cpp  
// https://github.com/boostorg/compute/issues/550  
BOOST_AUTO_TEST_CASE(count_issue_550)  
{  
    compute::vector<compute::uchar_> vec(1024*1024, compute::uchar_(1), queue);  
    compute::vector<compute::uchar_> vec2(1024*1024, compute::uchar_(1), queue);  
  
    BOOST_CHECK_EQUAL(  
        compute::count_if(  
            compute::make_zip_iterator(boost::make_tuple(vec.begin(), vec2.begin())),  
            compute::make_zip_iterator(boost::make_tuple(vec.end(), vec2.end())),  
            tuple_is_equal,  
            queue  
        ),  
        size_t(1024*1024)  
    );  
}  
```  
  
This code runs perfectly fine for me.   
  
EDIT:  
  
> EDIT: It works on my Desktop, too. That's embarrassing, sorry. I must have accidentally fixed some bug I > had while working at on my laptop. Thanks for your help!  
  
Oh, I didn't see that when I posted this comment. No problem! Good luck!
